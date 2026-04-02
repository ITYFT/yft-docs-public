# Root Models

## BidAsk

The `BidAsk` message provides the latest bid and ask prices for a specific financial instrument.

```json
{
    "id": "string",
    "bid": "double",
    "ask": "double",
    "date": "uint64",
}
```

| Name | Description                                              |
|------|----------------------------------------------------------|
| id   | Unique identifier of the currency pair (e.g., "BTCUSD"). |
| bid  | Bid price, the price buyers are willing to pay.          |
| ask  | Ask price, the price sellers are asking.                 |
| date | Timestamp of the quote (in Unix ms).                     |

## Account

Represents an account with all its details.

```json
{
   "id": "string",
   "traderid": "string",
   "currency": "string",
   "balance": "double",
   "tradingdisabled": "bool",
   "leverage": "double",
   "tradinggroup": "string",
   "stopout": "double",
   "createdate": "uint64",
   "lastupdatedate": "uint64",
   "lastupdateprocessid": "string",
   "metadata": { "string": "string" }
}
```

| Field Name             | Description                                           |
|------------------------|-------------------------------------------------------|
| id                     | Unique identifier of the account.                     |
| trader_id              | Identifier of the trader associated with the account. |
| currency               | Currency of the account (e.g., USD, EUR).             |
| balance                | Current balance of the account.                       |
| trading_disabled       | Indicates if trading is disabled for this account.    |
| leverage               | Leverage applied to the account.                      |
| trading_group          | The trading group this account belongs to.            |
| create_date            | Timestamp of account creation (Unix ms).              |
| last_update_date       | Last update timestamp of the account (Unix ms).       |
| last_update_process_id | Process ID of the last update made to the account.    |
| metadata (HashMap)     | Additional key-value pairs for extra information.     |

## Balance Operation

Represents a balance operation with all its details.

```json
{
    "id": "string",
    "traderid": "string",
    "accountid": "string",
    "operationtype": "string",
    "processid": "string",
    "delta": "double",
    "datetimeunixms": "uint64",
    "comment": "string",
    "referenceoperationid": "string"
}
```

| Name                              | Description                                                                                             |
|-----------------------------------|---------------------------------------------------------------------------------------------------------|
| id                                | Unique identifier of the operation.                                                                     |
| trader_id                         | Identifier of the trader associated with this operation.                                                |
| account_id                        | Identifier of the account involved in this operation.                                                   |
| operation_type                    | Type of balance operation. Enum: Trading; BalanceCorrection; Withdrawal; Deposit; Commission; Transfer; |
| process_id (Optional)             | Unique process identifier associated with the operation.                                                |
| delta                             | Change in balance due to the operation.                                                                 |
| date_time_unix_ms                 | Unix timestamp in milliseconds of when the operation occurred.                                          |
| comment (Optional)                | Optional comment describing the operation.                                                              |
| reference_operation_id (Optional) | Identifier of a related operation, if applicable.                                                       |

## Position

Represents an active position with all its details.

```json
{
  "id": "string",
  "traderid": "string",
  "accountid": "string",
  "assetpair": "string",
  "isbuy": "bool",
  "createdate": "uint64",
  "tpprice": "double or null",
  "slprice": "double or null",
  "createprocessid": "string",
  "profit": "double",
  "metadata": {
    "string": "string"
  },
  "lastupdatedate": "uint64",
  "lastupdateprocessid": "string",
  "assetopenbidask": "BidAskJsonModel",
  "opendate": "uint64",
  "openprocessid": "string",
  "closedate": "uint64 or null",
  "closereason": "PositionCloseReason",
  "assetclosebidask": "BidAskJsonModel or null",
  "closeprocessid": "string or null",
  "collateralcurrency": "string",
  "marginbidask": "BidAskJsonModel",
  "profitbidask": "BidAskJsonModel",
  "swaps": "PositionSwapJsonModel or null",
  "lotsamount": "double",
  "commission": "double"
}
```

| Name                   | Description                                                                                      |
|------------------------|--------------------------------------------------------------------------------------------------|
| id                     | Unique identifier for the position.                                                              |
| trader_id              | Identifier of the trader associated with the position.                                           |
| account_id             | Identifier of the account associated with the position.                                          |
| asset_pair             | Currency pair for the position (e.g., "BTCUSD").                                                 |
| is_buy                 | Indicates the position side.                                                                     |
| create_date            | Timestamp of the position creation (Unix ms).                                                    |
| tp_price               | Take profit price in instrument currency or null if not set.                                     |
| sl_price               | Stop loss price in instrument currency or null if not set.                                       |
| create_process_id      | Process ID that created this position.                                                           |
| profit                 | Profit generated by this position.                                                               |
| metadata               | Additional key-value pairs for extra information.                                                |
| last_update_date       | Timestamp of the last update to this position (Unix ms).                                         |
| last_update_process_id | Process ID of the last update.                                                                   |
| asset_open_bid_ask     | Bid and ask details at the time of position opening.                                             |
| open_date              | Timestamp of the position opening (Unix ms).                                                     |
| open_process_id        | Process ID for the opening of the position.                                                      |
| close_date             | Timestamp when the position was closed, if applicable.                                           |
| close_reason           | Reason for closing the position. Enum: ClientCommand; StopOut; TakeProfit; StopLoss; ForceClose; |
| asset_close_price      | Close price of the asset if the position is closed.                                              |
| asset_close_bid_ask    | Bid/ask price of the asset when the position was closed, as defined by  BidAskJsonModel .        |
| close_process_id       | Process ID associated with the position closure, if applicable.                                  |
| collateral_currency    | Currency used as collateral.                                                                     |
| margin_bid_ask         | Bid/ask prices relevant for margin, as defined by  BidAskJsonModel .                             |
| profit_bid_ask         | Bid and ask prices relevant for profit, as defined by  BidAskJsonModel .                         |
| swaps                  | List of swap details as defined by PositionSwapJsonModel .                                       |
| lots_amount            | Total amount of lots in the position.                                                            |
| commission             | Commission fees charged for this position.                                                       |

## Order

Represents a order, with all its details.

```json
{
  "id": "string",
  "traderid": "string",
  "accountid": "string",
  "assetpair": "string",
  "lotsamount": "double",
  "isbuy": "bool",
  "createdate": "uint64",
  "tpprice": "double or null",
  "slprice": "double or null",
  "createprocessid": "string",
  "metadata": { "string": "string" },
  "lastupdatedate": "uint64",
  "lastupdateprocessid": "string",
  "collateralcurrency": "string",
  "desireprice": "double",
  "ordertype": "PendingOrderType",
}
```

**Fields:**

| Name                   | Description                                                        |
|------------------------|--------------------------------------------------------------------|
| id                     | Unique identifier of the pending position.                         |
| trader_id              | Identifier of the trader associated with the pending position.     |
| account_id             | Identifier of the account associated with the pending position.    |
| asset_pair             | Currency pair for the pending position (e.g., "BTCUSD").           |
| lots_amount            | Amount of lots requested in the pending position.                  |
| is_buy                 | Indicates the position side;                                       |
| create_date            | Timestamp when the pending position was created (Unix ms).         |
| tp_price               | Take profit price in instrument currency or null if not set.       |
| sl_price               | Stop loss price in instrument currency or null if not set.         |
| create_process_id      | Process ID that created this pending position.                     |
| metadata               | Additional key-value pairs for extra information.                  |
| last_update_date       | Timestamp of the last update to this pending position (Unix ms).   |
| last_update_process_id | Process ID of the last update made to this pending position.       |
| collateral_currency    | Currency used as collateral for this position.                     |
| desire_price           | Desired price for executing the position.                          |
| order_type             | Type of order for the pending position; Enum: Stop, Limit; Market; |

## PositionSwapJsonModel

Defines the details of a swap operation on a position.

```json
{
    "amount": "double",
    "date": "uint64"
}
```

**Fields:**

| Name   | Type   | Description                                        |
|--------|--------|----------------------------------------------------|
| amount | double | Amount of the position swap.                       |
| date   | uint64 | Timestamp of the swap in Unix time (milliseconds). |