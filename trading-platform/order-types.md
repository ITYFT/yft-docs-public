# Order Types

Our platform supports the following order types:

## Market Orders

- Executes immediately at the current market price.
- **Use Case**: Ideal for fast-moving markets when you need immediate execution.

## Limit Orders

- Executes only when the market reaches a specified price or better.
- If the price set in a limit order is worse than the current market price, the order will execute as a market order.
- **Use Case**: Useful for entering or exiting trades at a specific level, ensuring better control over price.

## Stop Orders

- Triggers an order when the market reaches a specified price.
- **Use Case**: Often used for stop-losses or breakout trades.

## When to Use Limit vs Stop Orders

- **Limit Orders**: Use when you anticipate a price retracement before execution.
- **Stop Orders**: Use when entering on momentum or as a safeguard against losses.

## Take Profit and Stop Loss Relationship

- **Stop Loss**: A Stop Loss is essentially a **Stop Order** that triggers a market order to close a position when the market price reaches a specified level, minimizing potential losses.
- **Take Profit**: A Take Profit is essentially a **Limit Order** that closes a position when the market reaches the specified price, locking in profits.

## Pending Orders and Expiry Time

Pending orders can remain active for a specified period or until manually canceled. Our platform supports the following time validity settings:

1. **Good Till Cancelled (GTC)**:
      - The order remains active indefinitely until it is manually canceled by the trader.
      - Ideal for traders who want their order to persist regardless of time constraints.
2. **Good Till Date/Time (GTD)**:
      - The order remains active until a specified date and time.
      - Useful for traders who want their order to expire automatically after a specific period, aligning with their strategy.

These time validity options give traders complete control over their pending orders, ensuring flexibility and precision in execution.
## Trailing Stop Loss

A trailing stop is not a separate order type — it is a Stop Loss that the engine moves for
you, and it can be attached to any position, and to any pending order so that it applies to
the position that order opens.

**How the distance is set.** Trailing always requires a Stop Loss. The gap between that
Stop Loss and the current price is measured once, at the moment trailing is switched on,
and then held fixed.

**How it moves.**

| Position | Price moves | The stop |
| --- | --- | --- |
| Buy | Up to a new high | Follows up, keeping the same distance below |
| Buy | Down | Stays put |
| Sell | Down to a new low | Follows down, keeping the same distance above |
| Sell | Up | Stays put |

Only a new best price moves the stop; movement inside the range already seen does nothing,
and the stop is never moved backwards. When price reaches it the position closes as an
ordinary Stop Loss exit.

**Together with other features.** A partial close does not disturb trailing — it keeps
running on the volume that is left. Turning trailing off leaves the Stop Loss at the level
it last reached.

See [Web Interface: Trailing Stop Loss](web-interface-ui-ux-and-features.md#trailing-stop-loss)
for how it looks in the terminal.
