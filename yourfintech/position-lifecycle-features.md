# Position lifecycle features

Three features change how a position behaves after it is opened: **Partial Close**,
**Trailing Stop Loss** and **Reverse Position**. They are not three variants of the same
mechanism — one reduces a position's volume, one moves its stop loss automatically, and
one is a client-side composition of two ordinary operations. This page describes each of
them once, and states which integration surface exposes it.

## Availability by integration surface

| Feature | Integration API (HTTP) | Socket Integration | Yft Manager API | Trading Platform Integration (gRPC) |
| --- | --- | --- | --- | --- |
| Partial Close | `POST /api/v1/integration/positions/close` with `lots_to_close` | Reported in position state | `close_position` with `lots_to_close` | Not available |
| Trailing Stop Loss | `POST /api/v1/integration/positions/update` with `is_trailing_sl` | Reported in position and order state | `update_position`, `place_order`, `update_order` with `is_trailing_sl` | Not available |
| Reverse Position | No dedicated call — compose it | Reported as the resulting close and open | No dedicated call — compose it | Not available |

The `EngineIntegrationGrpcService` contract under
[Trading Platform Integration](trading-platform-integration/README.md) predates these
features and exposes none of them.

---

## Partial Close

A partial close reduces the volume of an open position instead of exiting the market
completely. It is requested through the ordinary close call, by supplying the volume to
close.

**The position survives.** It keeps its `id` and stays open with the remaining volume;
no new position is created and no position id changes. This is the single most important
property for an integrator: a partial close arrives as a position *update*, not as a
position *close*.

### What changes on the position

| Field | Effect |
| --- | --- |
| `lots_amount` | Reduced by the closed volume. |
| `initial_lots_amount` | Unchanged — still the volume the position was opened with. |
| `realized_pl` | Accumulates the result realised by each partial close. |
| `realized_swap` | Accumulates the swap settled by each partial close. *(Yft Manager API)* |

`initial_lots_amount - lots_amount` is the volume closed so far.

A separate closing order is created for the closed slice, carrying `is_close: true` and
the side opposite to the position.

### What an integrator observes

No closed-position record is produced, because the position is still open. Do not wait
for a close event: a client that only reconciles on position close will never see the
volume change. Reconcile on `position_updated` instead.

The event order depends on how the instrument is executed.

**B-Book — settled synchronously, in one pass:**

1. `order_placed` — the closing order for the slice.
2. `order_executed` — the same order, once executed.
3. `trades_created` — the trade records for the closed slice, under the original position id.
4. `position_updated` — the position with its reduced `lots_amount`, update source `PartialClose`.
5. `account_updated` — the balance operation booking the realised result.

**A-Book — settled in two phases, because the execution price comes from the liquidity
provider:**

*Phase 1, immediately:* `position_updated` (reduced `lots_amount`, update source
`PartialClose`), then `order_placed`. The volume is already reduced at this point, but
nothing has been booked yet.

*Phase 2, once the provider confirms:* `order_executed`, `trades_created`,
`account_updated`, and a second `position_updated` carrying the updated `realized_pl`.

So on A-Book the volume reduction and the realised result arrive in **separate**
`position_updated` events, and a rejection by the provider restores the rejected volume.

> `PartialClose` also exists as a value of the close-reason enum
> (`ClosedPositionHttpCloseReason`, `PositionCloseReason`). It is set by the
> administrative position-management path, not by the partial close described here.

### Boundary cases

* `lots_to_close` **equal to** the remaining volume is executed as a normal full close,
  not as a partial one.
* `lots_to_close` **omitted or `null`** means a full close — this is the pre-existing
  behaviour and is unchanged.
* A trailing stop loss attached to the position **stays attached** and keeps tracking the
  reduced volume.

### Rejections

| Condition | Yft Manager API error | Integration API error |
| --- | --- | --- |
| Volume above the remaining volume | `lots_to_close_exceeds_current` | `LotsToCloseExceedsCurrent` |
| Volume below the instrument's minimum operation volume | `lots_too_low` | `LotsTooLow` |
| Remainder above zero but below the instrument's minimum operation volume | `remaining_lots_below_minimum` | `RemainingLotsBelowMinimum` |
| Account currently being liquidated by stop-out | `account_under_stop_out` | `AccountUnderStopOut` |

The minimum-operation-volume checks apply to **both** sides of the split: the slice being
closed and the remainder left behind. A position of 0.03 lots on an instrument with a
minimum of 0.02 cannot be partially closed at all — any split leaves one side too small.

---

## Trailing Stop Loss

A trailing stop loss is a stop loss the engine moves on the position's behalf as the
market moves in the position's favour. It never moves against the position.

### Enabling it

Trailing is switched with `is_trailing_sl` and always needs a stop loss price to derive
its distance from:

| `is_trailing_sl` | Result |
| --- | --- |
| `true` | Trailing is enabled. `sl_price` is **required** in the same request. |
| `false` | Trailing is disabled. |
| `null` / omitted | The current mode is kept. If the position already trails, `sl_price` is **required** and the offset is recalculated from it. |

### The offset

When trailing is enabled, the engine computes a fixed distance:

```
offset = | reference price − sl_price |
```

rounded to the instrument's price precision. The reference price is the position's own
**close price** — the bid for a buy position, the ask for a sell position — taken at the
moment trailing is enabled, or the open price for a position that opens with trailing
already on.

The offset is then fixed. It is reported back as `trailing_sl_offset`, and
`is_trailing_sl` reports whether trailing is active.

An offset that works out to zero or less is rejected with `invalid_trailing_sl_offset`;
requesting trailing without a stop loss price is rejected with
`trailing_sl_requires_sl_price`.

### How it moves

On every price update the engine compares the position's current close price with the
best close price seen so far:

* **Buy position** — if the price is *higher* than the best so far, the best is raised and
  the stop loss moves to `best − offset`.
* **Sell position** — if the price is *lower* than the best so far, the best is lowered and
  the stop loss moves to `best + offset`.

If the price does not beat the best so far, nothing happens. The stop loss is never moved
backwards: a recomputed level that would loosen the stop is discarded in favour of the
existing one. From there the position closes on the stop loss like any other, with close
reason `Sl` / `StopLoss`.

### On orders

Pending orders carry `is_trailing_sl` as well. Setting it on `place_order` or
`update_order` attaches trailing to the position the order opens, rather than to the
order itself.

---

## Reverse Position

Reversing means ending up holding the same volume in the opposite direction. **There is
no reverse endpoint.** It is composed from two ordinary calls, in order:

1. **Close** the position in full.
2. **Place a market order** for the same volume on the opposite side.

This is exactly what the trading terminal does, and an integrator who needs the same
behaviour should do the same. The consequences of it being two operations rather than one
are worth stating plainly:

* The two legs are **not atomic.** The close can succeed and the open fail — for example
  on insufficient margin, or if trading is disabled between the two calls. The account is
  then flat, not reversed. Handle the second call's failure explicitly.
* Realised P\&L is booked by the close, exactly as for any other close.
* The new position is a **new position** with a new id. It does not inherit the original's
  metadata, stop loss, take profit or trailing state.
* Stop loss and take profit are **not** carried over automatically. To reproduce the
  terminal's "copy TP/SL on reverse" behaviour, pass the intended `sl_price` and
  `tp_price` on the opening order yourself, mirrored for the new direction.

Reversing a whole symbol rather than one position follows the same shape: close every leg
on that symbol, then open one market order for the net volume in the opposite direction.
Because legs can carry different stop loss and take profit levels, nothing is copied over
in that case.
