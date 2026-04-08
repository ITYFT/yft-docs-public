# Root Models

# YftManagerApiIdFull

This object is used to represent an entity's identifier. Depending on the client's id_representation setting, it may contain both a UUID and a shorter Numeric ID. JSON Structure

JSON

```json
{
  "uuid": "string",
  "numid": "number (u64)" | null
}
```

| Field  | Type              | Description                                            |
|--------|-------------------|--------------------------------------------------------|
| uuid   | string            | The globally unique, primary identifier.               |
| num_id | number (optional) | The shorter, numeric version of the ID. May be  null . |

Експортувати в Таблиці

# ManagerApiAccount

Represents a trading account associated with a specific trader. Contains key information about balances, margin, leverage, group, and configuration parameters. Used in responses like `get_accounts`.

### JSON structure

```json
{
  "id": { "uuid": "string", "numid": "number | null" },
  "traderid": { "uuid": "string", "numid": "number | null" },
  "currency": "string",
  "balance": 0.0,
  "equity": 0.0,
  "margin": 0.0,
  "freemargin": 0.0,
  "marginlevel": 0.0,
  "leverage": 0.0,
  "tradinggroup": "string",
  "lastupdatedate": integer (timestamp in milliseconds),
  "metadata": {
    "key": "value"
  },
  "status": 0,
  "hedgemode": 0
}
```

### Field reference

| Field            | Type                           | Description                                                        |
|------------------|--------------------------------|--------------------------------------------------------------------|
| id               | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Unique identifier of the account.                                  |
| trader_id        | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Identifier of the trader owning this account.                      |
| currency         | string                         | Currency code (e.g. USD, EUR) for account balance.                 |
| balance          | number                         | Current balance of the account.                                    |
| equity           | number                         | Total equity (balance adjusted by open P&L).                       |
| margin           | number                         | Total used margin.                                                 |
| free_margin      | number                         | Available margin (equity - used margin).                           |
| margin_level     | number                         | Ratio of equity to margin. Used to monitor risk.                   |
| leverage         | number                         | Leverage ratio (e.g. 100 = 1:100).                                 |
| trading_group    | string                         | Identifier of the group this account belongs to.                   |
| last_update_date | integer  (timestamp)           | Last time the account was updated (milliseconds since epoch, UTC). |
| metadata         | object                         | Key-value metadata associated with this account.                   |
| status           | int                            | See  ManagerApiAccountStatus  enum below.                          |
| hedge_mode       | int                            | See  ManagerApiAccountHedgeMode  enum below.                       |

---

### ManagerApiAccountStatus

| Value | Enum Variant | Description                                    |
|-------|--------------|------------------------------------------------|
| 0     | Active       | Account is active and can be used for trading. |
| 1     | Disabled     | Account is disabled and cannot be used.        |

---

### ManagerApiAccountHedgeMode

| Value | Enum Variant | Description                                                   |
|-------|--------------|---------------------------------------------------------------|
| 0     | Hedge        | Allows multiple independent positions on the same instrument. |
| 1     | Netting      | Positions are merged into a net position.                     |

# ManagerApiAccountBalanceOperation

### Description

Represents a single operation that affected a trading account's balance — such as a trade, deposit, withdrawal, commission charge, or internal correction. This object is part of the `ManagerApiAccountBalanceUpdate` structure and provides audit-level detail about the change.

### JSON Structure

```json
{
  "id": "string",
  "traderid": { "uuid": "string", "numid": "number | null" },
  "accountid": { "uuid": "string", "numid": "number | null" },
  "reason": "trading | deposit | withdrawal | transfer | balancecorrection | commission",
  "processid": { "uuid": "string", "numid": "number | null" },
  "delta": "float",
  "date": integer (timestamp in milliseconds),
  "comment": "string | null",
  "referenceoperationid": { "uuid": "string", "numid": "number | null" },
}
```

## UpdateBalanceReason

### Available `reason` values for balance update

| Value                | Description                                            |
|----------------------|--------------------------------------------------------|
| "trading"            | Balance change caused by trading activity (e.g., P&L). |
| "deposit"            | Manual or system-triggered deposit to the account.     |
| "withdrawal"         | Withdrawal of funds from the account.                  |
| "transfer"           | Internal transfer between accounts.                    |
| "balance_correction" | Manual balance adjustment for corrections or fixes.    |
| "commission"         | Commission charges applied to the account.             |
| "withdrawal_cancel"  | Reversal of a previously submitted withdrawal.         |

### Field Reference

| Field                  | Type                           | Description                                                             |
|------------------------|--------------------------------|-------------------------------------------------------------------------|
| id                     | string                         | Unique identifier for this operation.                                   |
| trader_id              | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | ID of the trader who owns the affected account.                         |
| account_id             | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | ID of the account that was modified.                                    |
| reason                 | string  enum                   | Type of operation:  trading ,  deposit ,  withdrawal ,  transfer , etc. |
| process_id             | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Optional identifier of the process that performed the update.           |
| delta                  | float                          | The amount the balance was adjusted by (positive or negative).          |
| date                   | integer  (timestamp)           | Timestamp of when the operation occurred (UNIX epoch, milliseconds).    |
| comment                | string | null                  | Optional comment or metadata.                                           |
| reference_operation_id | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | ID of another operation this one refers to, if linked.                  |

---

# ManagerApiCollateral

Represents a collateral asset used in the financial system.
This model describes the type of asset that can be used as margin or for settlement purposes in trading operations.

### Example

```json
{
  "id": "string",
  "name": "string",
  "digits": 2,
}
```

### Fields

| Field  | Type   | Description                                                            |
|--------|--------|------------------------------------------------------------------------|
| id     | string | Unique identifier of the collateral asset (e.g., "USD", "BTC").        |
| name   | string | Human-readable name of the collateral (e.g., "US Dollar").             |
| digits | u32    | Number of decimal digits supported for this collateral (e.g., 2 or 8). |

# ManagerApiAccountCalculationUpdate

Describes recalculated values for a trading account.
Used in `calculate_updates` events to reflect changes in account-level metrics such as margin and equity.

### JSON structure:

```json
{
  "accountid": { "uuid": "string", "numid": "number | null" },
  "margin": 0.0,
  "equity": 0.0,
  "freemargin": 0.0,
  "marginlevel": 0.0
}
```

### Field Description:

| Field        | Type                           | Description                                           |
|--------------|--------------------------------|-------------------------------------------------------|
| account_id   | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Unique identifier of the account.                     |
| margin       | number                         | Currently used margin for the account.                |
| equity       | number                         | Current account equity.                               |
| free_margin  | number                         | Funds available for trading or margin use.            |
| margin_level | number                         | Calculated margin level (%), typically equity/margin. |

# ManagerApiAccountBalanceUpdate

### Description

This structure wraps a balance update event for a trading account. It includes the current state of the account after the change, as well as an optional reference to the balance-changing operation that triggered the update. This object is used inside `YftServerMessageType<ManagerApiAccountBalanceUpdate>` responses or updates.

### JSON Structure

```json
{
  "account": { / ManagerApiAccount / },
  "balanceoperation": { / ManagerApiAccountBalanceUpdateOperation / } | null
}
```

### Field Reference

| Field            | Type                                               | Description                                                         |
|------------------|----------------------------------------------------|---------------------------------------------------------------------|
| account          | ManagerApiAccount                                  | The updated account snapshot after the balance modification.        |
| balance_operatio | [ ManagerApiAccountBalanceUpdateOperation ] | null | The operation that caused the balance update, or  null  if unknown. |

# ManagerApiDayOffProfile

Defines a profile used to restrict trading on recurring weekly intervals. Each profile contains one or more "day-off" time windows during which trading is disabled. Typically used to configure trading breaks per instrument or group.

### JSON structure

```json
{
  "id": "string",
  "name": "string",
  "isdisabled": false,
  "daysoff": [
    {
      "dowfrom": 1,
      "timefrom": "HH:MM:SS",
      "dowto": 1,
      "timeto": "HH:MM:SS"
    }
  ]
}
```

### Field reference

| Field       | Type    | Description                                                                                   |
|-------------|---------|-----------------------------------------------------------------------------------------------|
| id          | string  | Unique identifier of the day-off profile.                                                     |
| name        | string  | Human-readable name of the profile.                                                           |
| is_disabled | boolean | If true, the profile is inactive and not enforced.                                            |
| days_off    | array   | A list of time windows representing when trading is restricted. See  ManagerApiDayOffWindow . |

---

# ManagerApiDayOffWindow

Represents a specific time window within a week when trading is blocked. These windows can span across multiple days.

### JSON structure

```json
{
  "dowfrom": 1,
  "timefrom": "HH:MM:SS",
  "dowto": 1,
  "timeto": "HH:MM:SS"
}
```

### Field reference

| Field     | Type   | Description                                                               |
|-----------|--------|---------------------------------------------------------------------------|
| dow_from  | int    | Start day of the restriction (0 = Sunday, 1 = Monday, ..., 6 = Saturday). |
| time_from | string | Start time of the restriction in  HH:MM:SS  format.                       |
| dow_to    | int    | End day of the restriction.                                               |
| time_to   | string | End time of the restriction in  HH:MM:SS  format.                         |

# ManagerApiMarkupProfile

Defines a markup configuration profile that maps trading instruments to specific bid/ask markups and optional spread restrictions. Used to apply price modifications for particular clients or trading groups.

### JSON structure

```json
{
  "id": "string",
  "name": "string",
  "disabled": false,
  "instruments": {
    "instrumentid": {
      "instrumentid": "string",
      "markupbid": 0,
      "markupask": 0,
      "minspread": 0.0,
      "maxspread": 2.5
    }
  }
}
```

### Field reference

| Field       | Type                                       | Description                                              |
|-------------|--------------------------------------------|----------------------------------------------------------|
| id          | string                                     | Unique identifier for the markup profile.                |
| name        | string                                     | Descriptive name of the profile.                         |
| disabled    | boolean                                    | If true, the profile is not applied.                     |
| instruments | object<string, ManagerApiMarkupInstrument> | Map of instrument IDs to their specific markup settings. |

---

# ManagerApiMarkupInstrument

Describes the markup and spread restrictions for a specific trading instrument within a markup profile.

### JSON structure

```json
{
  "instrumentid": "string",
  "markupbid": 0,
  "markupask": 0,
  "minspread": 0.0,
  "maxspread": 2.5
}
```

### Field reference

| Field         | Type        | Description                                                             |
|---------------|-------------|-------------------------------------------------------------------------|
| instrument_id | string      | ID of the trading instrument.                                           |
| markup_bid    | int         | Adjustment to the bid price. Negative values reduce the bid.            |
| markup_ask    | int         | Adjustment to the ask price. Negative values reduce the ask.            |
| min_spread    | float (opt) | Optional minimum allowed spread. If  null , no restriction is enforced. |
| max_spread    | float (opt) | Optional maximum allowed spread. If  null , no restriction is enforced. |

# ManagerApiBidAsk

Represents a real-time snapshot of bid and ask prices for a specific trading instrument at a given moment. This model is used in price-related server messages to communicate current market conditions.

### JSON structure

```json
{
  "id": "string",
  "bid": float,
  "ask": float,
  "date": integer (timestamp in milliseconds),
  "base": "string",
  "quote": "string"
}
```

### Field reference

| Field | Type                 | Description                                                         |
|-------|----------------------|---------------------------------------------------------------------|
| id    | string               | Identifier of the instrument or price feed (e.g. "EURUSD").         |
| bid   | float                | Current bid price.                                                  |
| ask   | float                | Current ask price.                                                  |
| date  | integer  (timestamp) | When the snapshot was taken (in milliseconds since UNIX epoch, UTC) |
| base  | string               | Base currency of the instrument (e.g. "EUR").                       |
| quote | string               | Quote currency of the instrument (e.g. "USD").                      |

---

# ManagerApiSwapModel

Each swap represents an overnight adjustment applied to the position.

### JSON structure

```json
{
  "delta": -0.10,
  "date": integer (timestamp in milliseconds)

}
```

### Field reference

| Field | Type                 | Description                                                       |
|-------|----------------------|-------------------------------------------------------------------|
| delta | float                | Swap amount (positive or negative).                               |
| date  | integer  (timestamp) | When the swap was applied (in milliseconds since UNIX epoch, UTC) |

# ManagerApiPosition

Represents a trading position (open or closed) managed by the Manager API.
This structure is used for both **active** and **closed** positions, making it a unified data model for all position-related data.
It includes information such as order metadata, bid/ask prices used in different contexts, and applied swap charges.

### JSON structure

```json
{
  "id": { "uuid": "string", "numid": "number | null" },
  "traderid": { "uuid": "string", "numid": "number | null" },
  "accountid": { "uuid": "string", "numid": "number | null" },
  "assetpair": "string",
  "collateral": "string",
  "lotsamount": 0.0,
  "isbuy": true,
  "slprice": 0.0,
  "tpprice": 0.0,
  "metadata": {
    "key": "value"
  },
  "orderid": { "uuid": "string", "numid": "number | null" },
  "pl": 0.0,
  "marginbidask": {
    "id": "string",
    "bid": 0.0,
    "ask": 0.0,
    "date": 0,
    "base": "string",
    "quote": "string"
  },
  "openbidask": {
    "id": "string",
    "bid": 0.0,
    "ask": 0.0,
    "date": 0,
    "base": "string",
    "quote": "string"
  },
  "profitbidask": {
    "id": "string",
    "bid": 0.0,
    "ask": 0.0,
    "date": 0,
    "base": "string",
    "quote": "string"
  },
  "activebidask": {
    "id": "string",
    "bid": 0.0,
    "ask": 0.0,
    "date": 0,
    "base": "string",
    "quote": "string"
  },
  "commission": 0.0,
  "swaps": [
    {
      "delta": 0.0,
      "date": 0
    }
  ],
  "createdate": 0,
  "updatedate": 0
}
```

### Field reference

| Field         | Type                           | Description                                              |
|---------------|--------------------------------|----------------------------------------------------------|
| id            | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Unique identifier of the position.                       |
| trader_id     | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | ID of the trader who owns the position.                  |
| account_id    | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | ID of the account associated with the position.          |
| asset_pair    | string                         | Traded asset pair (e.g. "EURUSD").                       |
| collateral    | string                         | Collateral currency used (e.g. "USD").                   |
| lots_amount   | float                          | Total number of lots.                                    |
| is_buy        | bool                           | true  if this is a buy position, otherwise  false .      |
| sl_price      | float  (opt)                   | Stop-loss price, if set.                                 |
| tp_price      | float  (opt)                   | Take-profit price, if set.                               |
| metadata      | object                         | Arbitrary key-value data.                                |
| order_id      | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Identifier of the opening order.                         |
| pl            | number                         | Current profit or loss calculated.                       |
| margin_bidask | ManagerApiBidAsk               | Bid/ask used for margin calculation.                     |
| open_bidask   | ManagerApiBidAsk               | Price when the position was opened.                      |
| profit_bidask | ManagerApiBidAsk               | Price used for PnL calculation.                          |
| active_bidask | ManagerApiBidAsk               | Most recent bid/ask price.                               |
| commission    | float                          | Total commission paid.                                   |
| swaps         | array                          | List of swap entries applied.                            |
| create_date   | integer  (timestamp)           | Timestamp in milliseconds since epoch (position created) |
| update_date   | integer  (timestamp)           | Timestamp in milliseconds since epoch (last update)      |

# ManagerApiTrade

Represents a completed trade execution recorded by the Manager API.
Each trade corresponds to a single execution event and contains details about the trader, account, instrument, and execution metrics such as price, volume, and fees.

**JSON Structure**

```json
{
  "id": { "uuid": "string", "numid": "number | null" },
  "traderid": { "uuid": "string", "numid": "number | null" },
  "accountid": { "uuid": "string", "numid": "number | null" },
  "assetpair": "string",
  "orderid": { "uuid": "string", "numid": "number | null" },
  "positionid": { "uuid": "string", "numid": "number | null" } | null,
  "price": 0.0,
  "pl": 0.0,
  "fee": 0.0,
  "lotsamount": 0.0,
  "createdate": "string",
  "createprocessid": integer (timestamp in milliseconds),
  "isbuy": true
}
```

**Field Reference**

| Field             | Type                                  | Description                                                  |
|-------------------|---------------------------------------|--------------------------------------------------------------|
| id                | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) )        | Unique identifier of the trade.                              |
| trader_id         | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) )        | Identifier of the trader who executed the trade.             |
| account_id        | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) )        | Identifier of the trading account used for the trade.        |
| asset_pair        | string                                | Identifier of the traded asset pair (e.g.  "ETHUSD" ).       |
| order_id          | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) )        | Identifier of the order that triggered this trade.           |
| position_id       | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) )
 (opt) | Identifier of the associated position, if applicable.        |
| price             | number                                | Executed price of the trade.                                 |
| pl                | number                                | Realized profit or loss (PL) from this trade.                |
| fee               | number                                | Commission or fee charged for this trade.                    |
| lots_amount       | number                                | Number of lots traded.                                       |
| create_date       | integer  (timestamp)                  | Execution timestamp in milliseconds since UNIX epoch (UTC)   |
| create_process_id | string                                | Identifier of the internal process that recorded this trade. |
| is_buy            | boolean                               | true  if buy trade,  false  if sell.                         |

# ManagerApiTradingGroup

Represents a group of trading accounts logically grouped under a shared configuration.
Each trading group references several configuration profiles that define how trading conditions, swaps, and market data apply to its members.

**JSON Structure**

```json
{
  "id": "string",
  "name": "string",
  "tradingprofileid": "string",
  "swapprofileid": "string",
  "mdeprofileid": "string",
  "tradingdisabled": true
}
```

**Field Reference**

| Field              | Type   | Description                                                                           |
|--------------------|--------|---------------------------------------------------------------------------------------|
| id                 | string | Unique identifier of the trading group.                                               |
| name               | string | Human-readable display name of the trading group.                                     |
| trading_profile_id | string | ID of the associated trading profile that defines leverage, allowed instruments, etc. |
| swap_profile_id    | string | ID of the swap profile defining overnight charge rules.                               |
| mde_profile_id     | string | ID of the Market Data Engine profile used for price feeds and aggregation.            |
| trading_disabled   | bool   | Indicates if trading is currently disabled for all accounts in this group.            |

## ManagerApiPositionCalculationUpdate

Describes recalculated values for a trading position.
Used in `calculate_updates` events to reflect real-time profit/loss changes.

### JSON structure:

```json
{
  "accountid": { "uuid": "string", "numid": "number | null" },
  "positionid": { "uuid": "string", "numid": "number | null" },
  "grosspl": 0.0
}
```

### Field Description:

| Field       | Type                           | Description                             |
|-------------|--------------------------------|-----------------------------------------|
| account_id  | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | ID of the account owning this position. |
| position_id | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Unique identifier of the position.      |
| gross_pl    | number                         | Gross profit or loss of the position.   |

# ManagerApiTradingInstrument

Defines the full configuration for a tradable instrument in the system.
This includes its pricing structure, tick precision, trading constraints, and schedule-related settings.

**JSON Structure**

```json
{
  "id": "string",
  "name": "string",
  "digits": 2,
  "base": "string",
  "quote": "string",
  "ticksize": 0.01,
  "swapscheduleid": "string (opt)",
  "groupid": "string",
  "weight": 0,
  "daytimeout": 60,
  "nighttimeout": 60,
  "tradingdisabled": true,
  "lotsize": 100,
  "dayoffprofilesid": ["string"]
}
```

**Field Reference**

| Field               | Type          | Description                                                          |
|---------------------|---------------|----------------------------------------------------------------------|
| id                  | string        | Unique identifier or symbol of the trading instrument.               |
| name                | string        | Full descriptive name of the instrument.                             |
| digits              | u32           | Number of decimal digits for price precision.                        |
| base                | string        | Base asset in the trading pair.                                      |
| quote               | string        | Quote currency used to price the instrument.                         |
| tick_size           | f64           | Minimum increment between prices.                                    |
| swap_schedule_id    | string (opt)  | Optional ID of the swap schedule for overnight fees.                 |
| group_id            | string        | Identifier of the trading group/category this instrument belongs to. |
| weight              | u64           | Sorting index used in UI views.                                      |
| day_timeout         | u64           | Trade timeout in seconds during the day session.                     |
| night_timeout       | u64           | Trade timeout in seconds during the night session.                   |
| trading_disabled    | bool          | Flag indicating if trading is disabled for this instrument.          |
| lot_size            | f64           | Default size of one trading lot.                                     |
| day_off_profiles_id | array[string] | List of IDs for day-off profiles restricting trade hours.            |

# ManagerApiTradingProfile

Defines the complete trading constraints and defaults used by a trading group or account.
This includes leverage options, collateral rules, and per-instrument configuration.

**JSON Structure**

```json
{
  "id": "string",
  "name": "string",
  "stopoutpercent": 50.0,
  "isabook": true,
  "margincallpercent": 80.0,
  "instruments": [
    {
      "id": "string",
      "minoperationvolume": 0.01,
      "maxoperationvolume": 100.0,
      "maxpositionvolume": 500.0,
      "openpositionmindelayms": 100,
      "openpositionmaxdelayms": 500,
      "instrumentmaxleverage": 100,
      "commissionperlot": 5.0
    }
  ],
  "leverages": [50.0, 100.0, 200.0],
  "defaultleverage": 100.0,
  "collateralcurrencies": ["USD", "EUR"],
  "initialdeposit": 1000.0,
  "hedgemargincoefficient": 0.5,
  "commissionperlot": 7.5
}
```

**Field Reference**

| Field                    | Type          | Description                                          |
|--------------------------|---------------|------------------------------------------------------|
| id                       | string        | Unique identifier of the trading profile.            |
| name                     | string        | Descriptive name of the profile.                     |
| stop_out_percent         | f64           | Percentage of margin level at which stop-out occurs. |
| is_a_book                | bool          | Execution model: true = A-Book, false = B-Book.      |
| margin_call_percent      | f64           | Threshold margin percentage triggering margin call.  |
| instruments              | array<object> | List of instrument-specific constraints.             |
| leverages                | array<f64>    | Allowed leverage values for accounts.                |
| default_leverage         | f64           | Default leverage assigned to new accounts.           |
| collateral_currencies    | array<string> | Supported collateral currencies.                     |
| initial_deposit          | f64           | Minimum deposit requirement.                         |
| hedge_margin_coefficient | f64           | Margin multiplier used for hedged positions.         |
| commission_per_lot       | f64           | Default commission per lot if not overridden.        |

---

# ManagerApiTradingProfileInstrument

Specifies operational and risk constraints for a single instrument under a trading profile.

**Json Structure**

```json
{
    "id": "string",
    "minoperationvolume": 0.01,
    "maxoperationvolume": 100.0,
    "maxpositionvolume": 500.0,
    "openpositionmindelayms": 100,
    "openpositionmaxdelayms": 500,
    "instrumentmaxleverage": 100,
    "commissionperlot": 5.0
}
```

**Field Reference**

| Field                      | Type      | Description                                             |
|----------------------------|-----------|---------------------------------------------------------|
| id                         | string    | Identifier or symbol of the instrument.                 |
| min_operation_volume       | f64       | Minimum volume for a trade.                             |
| max_operation_volume       | f64       | Maximum volume allowed per trade.                       |
| max_position_volume        | f64       | Max volume of all open positions per instrument.        |
| open_position_min_delay_ms | u64       | Minimum allowed delay between opens (in ms).            |
| open_position_max_delay_ms | u64       | Maximum allowed delay between opens (in ms).            |
| instrument_max_leverage    | f64       | Max leverage allowed for this instrument.               |
| commission_per_lot         | f64 (opt) | Optional override of default commission per traded lot. |

# ManagerApiOrder

Represents a trading order submitted by a trader, including its type, status, and execution parameters.

---

### JSON Structure

```json
{
  "id": { "uuid": "string", "numid": "number | null" },
  "traderid": { "uuid": "string", "numid": "number | null" },
  "accountid": { "uuid": "string", "numid": "number | null" },
  "assetpair": "string",
  "collateral": "string",
  "lotsamount": number,
  "isbuy": boolean,
  "slprice": number | null,
  "tpprice": number | null,
  "metadata": {
    "key": "string"
  },
  "ordertype": "market" | "stop" | "limit",
  "createdate": integer (timestamp in milliseconds),
  "updatedate": integer (timestamp in milliseconds),
  "desiredprice": number | null,
  "orderstatus": ManagerApiOrderStatus,
  "positionid": { "uuid": "string", "numid": "number | null" },
  "isclose": bool,
}
```

---

### Field Descriptions

| Field         | Type                           | Description                                                                                                |
|---------------|--------------------------------|------------------------------------------------------------------------------------------------------------|
| id            | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Unique identifier of the order.                                                                            |
| trader_id     | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Identifier of the trader who created the order.                                                            |
| account_id    | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Identifier of the account associated with the order.                                                       |
| asset_pair    | string                         | Identifier of the traded asset pair (e.g. "EURUSD").                                                       |
| collateral    | string                         | Collateral currency for the order (e.g. "USD").                                                            |
| lots_amount   | number (f64)                   | Number of lots requested in the order.                                                                     |
| is_buy        | boolean                        | Indicates if the order is for buying ( true ) or selling ( false ).                                        |
| sl_price      | number (f64)                   | Optional stop-loss price.                                                                                  |
| tp_price      | number (f64)                   | Optional take-profit price.                                                                                |
| metadata      | object                         | Arbitrary key-value metadata associated with the order.                                                    |
| order_type    | string (enum)                  | Type of the order:  market ,  stop , or  limit .
See  ManagerApiOrderType                                  |
| create_date   | integer (timestamp)            | Timestamp when the order was created (milliseconds since epoch)                                            |
| update_date   | integer (timestamp)            | Timestamp of the most recent update (milliseconds since epoch)                                             |
| desired_price | number (f64)                   | Desired price for execution (used for  limit  or  stop  orders only).                                      |
| order_status  | ManagerApiOrderStatus          | Current status of the order (e.g. Pending, Canceled, Executed, Rejected).                                  |
| position_id   | object ( [YftManagerApiIdFull](#yftmanagerapiidfull) ) | Identifier of the trader's position associated with this order after execution, if any.                    |
| is_close      | bool                           | is_close  — flag indicating the order is created to close an existing position, not to open or modify one. |

# ManagerApiOrderType

The `order_type` field defines the type of trading order to be executed. Each type behaves differently with regard to price execution and required fields.

**Supported Values:**

| Value  | Description                                                                         |
|--------|-------------------------------------------------------------------------------------|
| market | Executes immediately at the best available price. Ignores  desire_price .           |
| limit  | Executes only at the specified  desire_price  or better. Requires  desire_price .   |
| stop   | Becomes a market order once the  desire_price  is reached. Requires  desire_price . |

**Summary:**

- `market` orders are typically used for instant execution.
- `limit` orders are used when the trader wants a specific entry price or better.
- `stop` orders are often used for breakout strategies or to initiate positions after a price threshold is met.

# ManagerApiOrderStatus

The `order_status` field represents the current state of a trading order. It indicates whether the order is still active, has been successfully executed, or was terminated for another reason.

### Supported Values:

| Value    | Description                                                                           |
|----------|---------------------------------------------------------------------------------------|
| pending  | The order has been submitted and is waiting to be executed or canceled.               |
| canceled | The order was canceled by the trader or system before execution.                      |
| executed | The order was successfully filled at the requested or market conditions.              |
| rejected | The order was rejected by the system (e.g., invalid parameters or not enough margin). |

### Summary:

- `pending` → order is still active and waiting to be processed.
- `canceled` → order did not execute and was explicitly terminated.
- `executed` → order has been fully filled.
- `rejected` → order was invalid or could not be processed due to system/business rules.