# Root Models

Every message on this socket is JSON with **snake_case** keys, matching the field
names below exactly. Enum values are snake_case too — `client_command`, `take_profit`,
`partial_close` and so on.

## BidAsk

The `BidAsk` message provides the latest bid and ask prices for a specific financial instrument.

```json
{
  "id": "string",
  "bid": "double",
  "ask": "double",
  "date": "uint64"
}
```

**Fields:**

| Name | Description |
| --- | --- |
| id | Unique identifier of the instrument (e.g. "BTCUSD"). |
| bid | Bid price, the price buyers are willing to pay. |
| ask | Ask price, the price sellers are asking. |
| date | Timestamp of the quote (Unix ms). |

## Account

Represents an account with all its details.

```json
{
  "id": "string",
  "trader_id": "string",
  "currency": "string",
  "balance": "double",
  "trading_disabled": "bool",
  "leverage": "double",
  "trading_group": "string",
  "create_date": "uint64",
  "last_update_date": "uint64",
  "last_update_process_id": "string",
  "metadata": { "string": "string" }
}
```

**Fields:**

| Name | Description |
| --- | --- |
| id | Unique identifier of the account. |
| trader_id | Identifier of the trader who owns the account. |
| currency | Account currency (e.g. "USD"). |
| balance | Current balance of the account. |
| trading_disabled | Indicates if trading is disabled for this account. |
| leverage | Leverage applied to the account. |
| trading_group | The trading group this account belongs to. |
| create_date | Timestamp of account creation (Unix ms). |
| last_update_date | Timestamp of the last update (Unix ms). |
| last_update_process_id | Process ID of the last update made to the account. |
| metadata | Additional key-value pairs for extra information. |

## Balance Operation

Represents a balance operation with all its details.

```json
{
  "id": "string",
  "trader_id": "string",
  "account_id": "string",
  "operation_type": "AccountBalanceOperationJsonType",
  "process_id": "string or null",
  "delta": "double",
  "date_time_unix_ms": "uint64",
  "comment": "string or null",
  "reference_operation_id": "string or null"
}
```

**Fields:**

| Name | Description |
| --- | --- |
| id | Unique identifier of the operation. |
| trader_id | Identifier of the trader associated with this operation. |
| account_id | Identifier of the account involved in this operation. |
| operation_type | Type of balance operation. Enum: trading; balance_correction; withdrawal; deposit; commission; transfer. |
| process_id | Process identifier associated with the operation. |
| delta | Change in balance produced by the operation. |
| date_time_unix_ms | When the operation occurred (Unix ms). |
| comment | Optional comment describing the operation. |
| reference_operation_id | Identifier of a related operation, if any. |

## Position

Represents a position with all its details. The same model is used for a position that is open and for one that has been closed — `close_date`, `close_reason`, `asset_close_bid_ask` and `close_process_id` are `null` while it is open.

```json
{
  "id": "string",
  "trader_id": "string",
  "account_id": "string",
  "asset_pair": "string",
  "is_buy": "bool",
  "create_date": "uint64",
  "tp_price": "double or null",
  "sl_price": "double or null",
  "create_process_id": "string",
  "profit": "double or null",
  "metadata": { "string": "string" },
  "last_update_date": "uint64",
  "last_update_process_id": "string",
  "asset_open_bid_ask": "BidAskJsonModel or null",
  "open_date": "uint64",
  "open_process_id": "string",
  "close_date": "uint64 or null",
  "close_reason": "PositionCloseReason or null",
  "asset_close_bid_ask": "BidAskJsonModel or null",
  "close_process_id": "string or null",
  "collateral_currency": "string",
  "margin_bid_ask": "BidAskJsonModel or null",
  "profit_bid_ask": "BidAskJsonModel or null",
  "swaps": ["PositionSwapJsonModel"],
  "lots_amount": "double",
  "commission": "double or null",
  "is_trailing_sl": "bool",
  "realized_pl": "double or null",
  "initial_lots_amount": "double"
}
```

**Fields:**

| Name | Description |
| --- | --- |
| id | Unique identifier of the position. |
| trader_id | Identifier of the trader who owns the position. |
| account_id | Identifier of the account the position belongs to. |
| asset_pair | Traded instrument (e.g. "BTCUSD"). |
| is_buy | `true` for a buy position, `false` for a sell. |
| create_date | Timestamp of position creation (Unix ms). |
| tp_price | Take profit price in instrument price, or `null` if not set. |
| sl_price | Stop loss price in instrument price, or `null` if not set. Moved by the engine while `is_trailing_sl` is `true`. |
| create_process_id | Process ID that created this position. |
| profit | Current profit or loss of the position. |
| metadata | Additional key-value pairs for extra information. |
| last_update_date | Timestamp of the last update (Unix ms). |
| last_update_process_id | Process ID of the last update. |
| asset_open_bid_ask | Bid/ask at the moment the position was opened. |
| open_date | Timestamp of the position opening (Unix ms). |
| open_process_id | Process ID of the opening. |
| close_date | Timestamp of the close, or `null` while the position is open. |
| close_reason | Why the position was closed, or `null` while it is open. |
| asset_close_bid_ask | Bid/ask at the moment the position was closed. |
| close_process_id | Process ID of the close, if closed. |
| collateral_currency | Currency used as collateral. |
| margin_bid_ask | Bid/ask used for the margin calculation. |
| profit_bid_ask | Bid/ask used for the profit calculation. |
| swaps | Swap charges applied to the position. |
| lots_amount | Remaining lots. A partial close lowers this; `initial_lots_amount` stays as it was at open. |
| commission | Commission charged for this position. |
| is_trailing_sl | `true` while a trailing stop loss is attached. The engine then moves `sl_price` on every new best price in the position's favour, and never moves it back. |
| realized_pl | Profit or loss already realised by partial closes, or `null` if nothing has been partially closed. |
| initial_lots_amount | Lots the position was opened with. `initial_lots_amount - lots_amount` is the volume closed so far. |

### PositionCloseReason

| Value | Meaning |
| --- | --- |
| `client_command` | Closed on request. |
| `stop_out` | Closed by stop-out liquidation. |
| `take_profit` | Closed by take profit. |
| `stop_loss` | Closed by stop loss, including a stop loss moved there by trailing. |
| `force_close` | Closed by an administrative action. |
| `partial_close` | Set by the administrative position-management path. A partial close requested through `lots_to_close` leaves the position open and produces no closed position, so it does not appear here. |

See [Position lifecycle features](../../position-lifecycle-features.md) for how partial
closes and trailing stops change a position.

## Order

Represents a pending order with all its details.

```json
{
  "id": "string",
  "trader_id": "string",
  "account_id": "string",
  "asset_pair": "string",
  "lots_amount": "double",
  "is_buy": "bool",
  "create_date": "uint64",
  "tp_price": "double or null",
  "sl_price": "double or null",
  "create_process_id": "string",
  "metadata": { "string": "string" },
  "last_update_date": "uint64",
  "last_update_process_id": "string",
  "collateral_currency": "string",
  "desire_price": "double or null",
  "order_type": "PendingOrderType",
  "position_id": "string or null",
  "is_trailing_sl": "bool"
}
```

**Fields:**

| Name | Description |
| --- | --- |
| id | Unique identifier of the pending order. |
| trader_id | Identifier of the trader who placed the order. |
| account_id | Identifier of the account the order belongs to. |
| asset_pair | Traded instrument (e.g. "BTCUSD"). |
| lots_amount | Lots requested by the order. |
| is_buy | `true` for a buy order, `false` for a sell. |
| create_date | Timestamp of order creation (Unix ms). |
| tp_price | Take profit price in instrument price, or `null` if not set. |
| sl_price | Stop loss price in instrument price, or `null` if not set. |
| create_process_id | Process ID that created this order. |
| metadata | Additional key-value pairs for extra information. |
| last_update_date | Timestamp of the last update (Unix ms). |
| last_update_process_id | Process ID of the last update. |
| collateral_currency | Currency used as collateral. |
| desire_price | Price at which the order should execute. |
| order_type | Order type. Enum: stop; limit; market. |
| position_id | Position this order belongs to once executed; `null` while it is still pending. |
| is_trailing_sl | `true` when the order carries a trailing stop loss that activates on the position it opens. |

### PendingOrderType

| Value | Meaning |
| --- | --- |
| `stop` | Executes once the market reaches `desire_price`. |
| `limit` | Executes at `desire_price` or better. |
| `market` | Executes at the current market price. |

## PositionSwapJsonModel

Defines a single swap charge applied to a position.

```json
{
  "amount": "double",
  "date": "uint64"
}
```

**Fields:**

| Name | Description |
| --- | --- |
| amount | Amount of the swap charge. |
| date | When the swap was applied (Unix ms). |
