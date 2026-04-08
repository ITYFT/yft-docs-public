# Client Messages spec

This section describes all message types that a client can send to the server over the TCP socket. All client messages are encapsulated inside the `message_type.client_message` field in the `YftManagerApiMessage` structure.

## YftManager API Client Messages

The `YftManagerApiClientMessage` enum defines the possible messages that can be sent to the YftManager API client. Below is a list of the available message types:

* **AuthRequest**: Initiates an authentication request.
* **GetServerTime**: Retrieves the current server time.
* **GetAccounts**: Requests account information.
* **GetPositions**: Fetches current positions.
* **ClosePosition**: Requests to close an open trading position.
* **UpdatePosition**: Updates SL/TP or metadata of an existing position.
* **GetLastPrices**: Obtains the latest prices.
* **UpdateBalance**: Updates the account balance.
* **GetHistoryPositions**: Retrieves historical positions.
* **GetOrders**: Requests order details.
* **PlaceOrder**: Sends a request to place a new order.
* **CancelOrder**: Sends a request to cancel an existing order.
* **UpdateOrder**: Sends a request to update an existing order (e.g., price, SL/TP, lots).
* **GetTrades**: Retrieves trade information.
* **Subscribe**: Subscribes to specified updates.

## Metadata Structure

```json
{
  "mode": "merge",
  "metadata": {
    "key1": "value1",
    "key2": "value2"
  }
}
```

**Mode**

| Value    | Description                                                             |
| -------- | ----------------------------------------------------------------------- |
| `merge`  | Merge new fields into existing metadata. Existing keys will be updated. |
| `update` | Replace all existing metadata with the new set.                         |

## AuthRequest

This message is used to authenticate the client. It must be sent as the first message after establishing a TCP connection.

#### JSON Structure

Input Example:

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "auth_request": {
        "secret_key": "string",
        "id_representation": "uuid_only"
      }
    }
  }
}
```

| Field                | Type            | Description (EN)                                                                    |
| -------------------- | --------------- | ----------------------------------------------------------------------------------- |
| `secret_key`         | `string`        | Secret key for authenticating client                                                |
| `id_representation`  | `string` (enum) | Defines the preferred format for identifiers in server responses. See values below. |

This structure is used for client authentication requests by providing a secret key. Ensure that `message_id` and `secret_key` are filled with the appropriate values.

#### Enum Values of `id_representation`

This field determines how identifiers (like `account_id`, `position_id`, etc.) will be represented in messages sent from the server *to this client*.

| Value              | Description                                                                        |
| ------------------ | ---------------------------------------------------------------------------------- |
| `uuid_only`        | (Default) The server will only send the full UUID for all identifiers.             |
| `num_id_preferred` | The server will send both the UUID and the shorter Numeric ID for all identifiers. |


## GetServerTime

This message is used by the client to request the current server time.

\
JSON Structure

```json
{
  "message_id": "string",
  "message_type": {
    "client_message": {
      "get_server_time": {}
    }
  }
}
```

#### Parameters

| Field            | Type   | Description                                                        |
| ---------------- | ------ | ------------------------------------------------------------------ |
| `message_id`     | string | A unique identifier for this message (typically a UUID).           |
| `client_message` | object | Must contain a key `"get_server_time"` with empty object as value. |

> `get_server_time` is now a structured variant of the `YftManagerApiClientMessage` enum and must be serialized as an object with an empty payload: `{}`.

## GetAccounts

This message is used to request a list of trading accounts. The client may optionally filter by `trader_id` or a specific `account_id`.

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "get_accounts": {
        "trader_id": { "uuid": "string" } | null, // Or { "id": number }
        "account_id": { "uuid": "string" } | null  // Or { "id": number }
      }
    }
  }
}
```

#### Field Explanation

| Field         | Type                            | Description                                                           |
| ------------- | ------------------------------- | --------------------------------------------------------------------- |
| `trader_id`   | `UUID\|Numeric ID` (optional)   | If provided, only accounts belonging to this trader will be returned. |
| `account_id`  | `UUID\|Numeric ID` (optional)   | If provided, only the specific account will be returned.              |

## GetPositions

This message is used to request a list of currently open trading positions. The client can filter results by `trader_id`, `account_id`, or `asset_pair`.

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "get_positions": {
        "position_id": { "uuid": "string" } | null, // Or { "id": number }
        "trader_id": { "uuid": "string" } | null,   // Or { "id": number }
        "account_id": { "uuid": "string" } | null,  // Or { "id": number }
        "asset_pair": "string" | null
      }
    }
  }
}
```

#### Field Explanation

| Field          | Type                          | Description                                                        |
| -------------- | ----------------------------- | ------------------------------------------------------------------ |
| `position_id`  | `UUID\|Numeric ID` (optional) | Filter by a specific position ID.                                  |
| `trader_id`    | `UUID\|Numeric ID` (optional) | Filter positions by trader ID.                                     |
| `account_id`   | `UUID\|Numeric ID` (optional) | Filter positions by account ID.                                    |
| `asset_pair`   | `string` (opt)                | Filter positions by the symbol of the asset pair (e.g., "eurusd"). |

## ClosePosition

#### Description

Closes an open trading position (full exit from the market). The system calculates PnL based on the current market price.

***

#### JSON Request Example

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "close_position": {
        "trader_id": { "uuid": "string" }, // Or { "id": number }
        "account_id": { "uuid": "string" },// Or { "id": number }
        "position_id": { "uuid": "string" }, // Or { "id": number }
        "process_id": "string",
        "force": true, // Or false or null
      }
    }
  }
}
```

***

#### Fields

| Field         | Type               | Required | Description                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------- | ------------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `trader_id`   | `UUID\|Numeric ID` | Yes      | Trader's unique ID.                                                                                                                                                                                                                                                                                                                                                                                       |
| `account_id`  | `UUID\|Numeric ID` | Yes      | ID of the account where the position is open.                                                                                                                                                                                                                                                                                                                                                             |
| `position_id` | `UUID\|Numeric ID` | Yes      | ID of the position to close.                                                                                                                                                                                                                                                                                                                                                                              |
| `process_id`  | `string`           | Yes      | Unique ID to track the close request (idempotency).                                                                                                                                                                                                                                                                                                                                                       |
| `force`       | `bool`             | No       | **`false`**: Normal close with full validations. **`true`**: Admin override; bypasses `account_trading_disabled` gate (core checks still apply). **`none`**: Normal behaviour. |

***

#### Behavior

* If the position exists and is open, it will be closed using the best available market price.
* If `position_id` is invalid or already closed — an error will be returned.

## UpdatePosition

#### Description

Updates parameters (SL/TP, metadata) of an **open** position.

***

#### JSON Request Example

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "update_position": {
        "trader_id": { "uuid": "string" }, // Or { "id": number }
        "account_id": { "uuid": "string" },// Or { "id": number }
        "position_id": { "uuid": "string" },// Or { "id": number }
        "process_id": "string",
        "tp_price": "number (f64)" | null,
        "sl_price": "number (f64)" | null,
        "metadata": {
          "mode": "merge" | "update",
          "metadata": {
            "key1": "value1",
            "key2": "value2"
          }
        } | null
      }
    }
  }
}
```

***

#### Fields

| Field         | Type               | Required | Description                                                  |
| ------------- | ------------------ | -------- | ------------------------------------------------------------ |
| `trader_id`   | `UUID\|Numeric ID` | ✅        | Trader's unique ID.                                          |
| `account_id`  | `UUID\|Numeric ID` | ✅        | Account ID for the position.                                 |
| `position_id` | `UUID\|Numeric ID` | ✅        | ID of the position to update.                                |
| `process_id`  | string             | ✅        | Unique request ID for idempotency.                           |
| `tp_price`    | float              | ❌        | New take-profit value (optional).                            |
| `sl_price`    | float              | ❌        | New stop-loss value (optional).                              |
| `metadata`    | object             | ❌        | Optional metadata [Metadata Structure](#metadata-structure). |

## GetLastPrices

This message is used to request the latest bid/ask prices for asset pairs. The client may specify a particular `asset_pair` or leave it empty to retrieve all available prices.

#### JSON Structure

```json
{
  "message_id": "string",
  "message_type": {
    "client_message": {
      "get_last_prices": {
        "asset_pair": "string | null"
      }
    }
  }
}
```

#### Field Explanation

| Field         | Type           | Description                                                                                   |
| ------------- | -------------- | --------------------------------------------------------------------------------------------- |
| `asset_pair`  | `string` (opt) | Symbol of the asset pair to filter (e.g., "eurusd"). If omitted, all prices will be returned. |

## UpdateBalance

This request is used to change a trader’s account balance manually. It may represent a deposit, withdrawal, balance correction, or similar adjustment. The request requires authorization and a valid reason.

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "update_balance": {
        "trader_id": { "uuid": "string" }, // Or { "id": number }
        "account_id": { "uuid": "string" },// Or { "id": number }
        "delta": "number (f64)",
        "comment": "string" | null,
        "process_id": "string",
        "allow_negative_balance": "boolean",
        "reason": "string (enum)",
        "reference_transaction_id": "string" | null,
        "same_response_process_id": "boolean"
      }
    }
  }
}
```

#### Field Explanation

| Field                         | Type               | Description                                                               |
| ----------------------------- | ------------------ | ------------------------------------------------------------------------- |
| `trader_id`                   | `UUID\|Numeric ID` | ID of the trader.                                                         |
| `account_id`                  | `UUID\|Numeric ID` | ID of the account to update.                                              |
| `delta`                       | `number`           | The balance change amount. Positive for credit, negative for debit.       |
| `comment`                     | `string` (opt)     | Optional comment explaining the adjustment.                               |
| `process_id`                  | `string`           | Unique identifier for this process/request.                               |
| `allow_negative_balance`      | `boolean`          | Whether the update is allowed to cause a negative balance.                |
| `reason`                      | `string` (enum)    | Reason for the balance update (e.g., `deposit`, `withdrawal`, `trading`). |
| `reference_transaction_id`    | `string` (opt)     | Optional reference to another related transaction.                        |
| `same_response_process_id`    | `boolean`          | If true, the response will echo the same `process_id`.                    |

**`reason` enum**

| Variant              | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| `trading`            | Adjustments due to trading operations.                       |
| `deposit`            | Manual or external deposit.                                  |
| `withdrawal`         | Manual or external withdrawal.                               |
| `transfer`           | Funds moved between accounts.                                |
| `balance_correction` | Correction of balance due to error or administrative action. |

## GetHistoryPosition

This request retrieves a list of previously closed (historical) positions. It allows filtering by trader ID, account ID, and asset pair. This request is often used for reporting, audit, or analytics purposes.

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "get_history_positions": {
        "trader_id": { "uuid": "string" } | null, // Or { "id": number }
        "account_id": { "uuid": "string" } | null, // Or { "id": number }
        "asset_pair": "string" | null
      }
    }
  }
}
```

#### Field Explanation

| Field         | Type                      | Description                           |
| ------------- | ------------------------- | ------------------------------------- |
| `trader_id`   | `UUID\|Numeric ID` (opt)  | Filter by trader ID.                  |
| `account_id`  | `UUID\|Numeric ID` (opt)  | Filter by specific account ID.        |
| `asset_pair`  | `string` (opt)            | Filter by asset pair (e.g. `eurusd`). |

## GetOrders

This request retrieves a list of active orders. It can be filtered by trader ID, account ID, asset pair, or a specific position. This allows the client to get pending or currently open orders on the account.

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "get_orders": {
        "order_id": { "uuid": "string" },     // or { "id": number } or null 
        "trader_id": { "uuid": "string" },     // or { "id": number } or null
        "account_id": { "uuid": "string" },    // or { "id": number } or null
        "asset_pair": "string or null",
        "order_status": "ManagerApiOrderStatus | null"
      }
    }
  }
}

```

#### Field Description

| Field           | Type                       | Description                             |
| --------------- | -------------------------- | --------------------------------------- |
| `order_id`      | `UUID\|Numeric ID` (opt)   | Filter by order ID                      |
| `trader_id`     | `UUID\|Numeric ID` (opt)   | Filter by trader ID                     |
| `account_id`    | `UUID\|Numeric ID` (opt)   | Filter by account ID                    |
| `asset_pair`    | `string` (opt)             | Filter by asset pair (e.g., `"eurusd"`) |
| `order_status`  | [`ManagerApiOrderStatus`](root-models.md#managerapiorderstatus) | Filter by order status |

## PlaceOrder

#### Description

Sends a request to create a new trading order. The order can be of type `Market`, `Limit`, or `Stop`. Depending on the type, some fields like `desire_price` may be required or ignored.

***

#### JSON Request Example

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "place_order": {
        "trader_id": { "uuid": "string" }, // Or { "id": number }
        "account_id": { "uuid": "string" },// Or { "id": number }
        "asset_pair": "string",
        "lots_amount": "number (f64)",
        "is_buy": "boolean",
        "sl_price": "number (f64)" | null,
        "tp_price": "number (f64)" | null,
        "metadata": { "key": "value" } | null,
        "order_type": "market" | "limit" | "stop",
        "desire_price": "number (f64)" | null,
        "process_id": "string (uuid)"
      }
    }
  }
}
```

***

#### Fields

| Field          | Type                                | Required | Description                                                                             |
| -------------- | ----------------------------------- | -------- | --------------------------------------------------------------------------------------- |
| `trader_id`    | `UUID\|Numeric ID`                  | Yes      | Unique ID of the trader placing the order.                                              |
| `account_id`   | `UUID\|Numeric ID`                  | Yes      | ID of the account used to place the order.                                              |
| `asset_pair`   | `string`                            | Yes      | Symbol of the trading pair (e.g., "EURUSD").                                            |
| `lots_amount`  | `float`                             | Yes      | Size of the order in lots.                                                              |
| `is_buy`       | `bool`                              | Yes      | `true` for Buy orders, `false` for Sell.                                                |
| `sl_price`     | `float` or `null`                   | No       | Stop Loss price.                                                                        |
| `tp_price`     | `float` or `null`                   | No       | Take Profit price.                                                                      |
| `metadata`     | `object` or `null`                  | No       | Optional key-value metadata.                                                            |
| `order_type`   | `"market"` \| `"limit"` \| `"stop"` | Yes      | [Order type](root-models.md#managerapiordertype). |
| `desire_price` | `float` or `null`                   | No       | Target price for Limit or Stop orders. Ignored for Market.                              |
| `process_id`   | `string` (UUID)                     | Yes      | Unique ID to track this request (idempotency).                                          |

***

#### Expected Server Response

On success:

```json
{
  "message_id": "string",
  "message_response_id": "string",
  "message_type": {
    "server_message": {
      "place_order_response": {
        "success": {
          "id": "string",
          ...
        }
      }
    }
  }
}
```

On failure:

```json
{
  "message_id": "string",
  "message_response_id": "string",
  "message_type": {
    "server_message": {
      "place_order_response": {
        "error": "network_error" | "unexpected" | ...
      }
    }
  }
}
```

## UpdateOrder

The `UpdateOrder` message is used to modify the parameters of an existing pending order (e.g., limit or stop order). It allows updating SL/TP, desired price, lots, or metadata associated with the order.

#### Message Type

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "update_order": {
        "trader_id": { "uuid": "string" }, // Or { "id": number }
        "account_id": { "uuid": "string" },// Or { "id": number }
        "order_id": { "uuid": "string" },  // Or { "id": number }
        "process_id": "string",
        "tp_price": "number (f64)" | null,
        "sl_price": "number (f64)" | null,
        "desire_price": "number (f64)" | null,
        "lots_amount": "number (f64)" | null,
        "metadata": {
          "mode": "merge" | "update",
          "metadata": { "key": "value" }
        } | null
      }
    }
  }
}
```

#### Field Descriptions

| Field          | Type               | Required | Description                                                                    |
| -------------- | ------------------ | -------- | ------------------------------------------------------------------------------ |
| `trader_id`    | `UUID\|Numeric ID` | ✅        | Unique identifier of the trader.                                               |
| `account_id`   | `UUID\|Numeric ID` | ✅        | Identifier of the trading account.                                             |
| `order_id`     | `UUID\|Numeric ID` | ✅        | Identifier of the order to update.                                             |
| `process_id`   | string             | ✅        | Unique ID for tracing this update operation.                                   |
| `tp_price`     | float              | ❌        | New take-profit price. If `null`, TP remains unchanged.                        |
| `sl_price`     | float              | ❌        | New stop-loss price. If `null`, SL remains unchanged.                          |
| `desire_price` | float              | ❌        | New desired execution price (for limit or stop orders).                        |
| `lots_amount`  | float              | ❌        | New order size. If `null`, lot amount remains unchanged.                       |
| `metadata`     | object             | ❌        | Optional metadata update block. See [Metadata Structure](#metadata-structure). |

***

## GetTrades

This request retrieves a list of executed trades. You can filter them by trader ID, account ID, asset pair, position ID, or order ID. It allows clients to access the trade history related to specific entities.

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "get_trades": {
        "trader_id": { "uuid": "string" } | null,   // Or { "id": number }
        "account_id": { "uuid": "string" } | null,  // Or { "id": number }
        "asset_pair": "string" | null,
        "position_id": { "uuid": "string" } | null, // Or { "id": number }
        "order_id": { "uuid": "string" } | null    // Or { "id": number }
      }
    }
  }
}
```

#### Field Description

| Field          | Type                      | Description                                  |
| -------------- | ------------------------- | -------------------------------------------- |
| `trader_id`    | `UUID\|Numeric ID` (opt)  | Filter by trader ID                          |
| `account_id`   | `UUID\|Numeric ID` (opt)  | Filter by account ID                         |
| `asset_pair`   | `string` (opt)            | Filter by trading pair (e.g., `"btcusd"`)    |
| `position_id`  | `UUID\|Numeric ID` (opt)  | Filter trades related to a specific position |
| `order_id`     | `UUID\|Numeric ID` (opt)  | Filter trades related to a specific order    |

## CancelOrder

#### Description

Cancels an existing **pending** order (typically a limit or stop order). Once cancelled, the order will no longer be executed even if market conditions are met.

***

#### JSON Request Example

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "cancel_order": {
        "trader_id": { "uuid": "string" }, // Or { "id": number }
        "account_id": { "uuid": "string" }, // Or { "id": number }
        "order_id": { "uuid": "string" },   // Or { "id": number }
        "force": true, // Or false or null
      }
    }
  }
}
```

***

#### Fields

| Field        | Type               | Required | Description                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------ | ------------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `trader_id`  | `UUID\|Numeric ID` | Yes      | Unique identifier of the trader who created the order.                                                                                                                                                                                                                                                                                                                                              |
| `account_id` | `UUID\|Numeric ID` | Yes      | Identifier of the account where the order was placed.                                                                                                                                                                                                                                                                                                                                               |
| `order_id`   | `UUID\|Numeric ID` | Yes      | Identifier of the order to cancel.                                                                                                                                                                                                                                                                                                                                                                  |
| `force`      | `bool`             | No       | **`false`**: Normal cancel with full validations. **`true`**: Admin override; bypasses `account_trading_disabled` gate (core checks still apply). **`none`**: Normal behaviour. |

***

#### Success Response

```json
{
  "message_id": "string",
  "message_response_id": "string",
  "message_type": {
    "server_message": {
      "cancel_order_response": {
        "success": true
      }
    }
  }
}
```

***

#### Error Response

```json
{
  "message_id": "string",
  "message_response_id": "string",
  "message_type": {
    "server_message": {
      "cancel_order_response": {
        "error": "network_error" | "unexpected" | ...
      }
    }
  }
}
```

## GetCollaterals

This request retrieves a list of available collaterals. You can optionally filter them by collateral ID.\
It allows clients to access supported assets used for margin or balance-related operations.

#### JSON Structure

```json
{
  "message_id": "string",
  "message_type": {
    "client_message": {
      "get_collaterals": {
        "collateral_id": "string (opt)"
      }
    }
  }
}
```

***

#### Field Description

| Field           | Type         | Description                                                                                      |
| --------------- | ------------ | ------------------------------------------------------------------------------------------------ |
| `collateral_id` | string (opt) | Filter by specific collateral ID (e.g., `"BTC"`). If omitted, returns all available collaterals. |

## GetBalanceOperations

This request allows the client to retrieve **account balance operation history**.\
It supports filtering by trader, account, date range, reason, and reference IDs.

***

#### JSON Structure

```json
{
  "message_id": "string (uuid)",
  "message_type": {
    "client_message": {
      "get_balance_operations": {
        "trader_id": { "uuid": "string" } | null, // Or { "id": number }
        "account_id": { "uuid": "string" } | null, // Or { "id": number }
        "operation_id": "string" | null,
        "reference_operation_id": { "uuid": "string" } | null, // Or { "id": number }
        "operation_type": "string (enum)" | null,
        "datetime_from": "number (u64)" | null,
        "datetime_to": "number (u64)" | null
      }
    }
  }
}
```

***

#### Field Description

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `trader_id` | `UUID\|Numeric ID (opt)` | No | Filter by trader ID |
| `account_id` | `UUID\|Numeric ID (opt)` | No | Filter by account ID |
| `operation_id` | string | No | Filter by specific balance operation ID |
| `reference_operation_id` | string | No | Filter by a referenced/linked operation |
| `operation_type` | string | No | Filter by type: `trading`, `deposit`, `withdrawal`, `transfer`, etc. See [UpdateBalanceReason](root-models.md#updatebalancereason) |
| `datetime_from` | number | No | Start of the date filter (timestamp in milliseconds) |
| `datetime_to` | number | No | End of the date filter (timestamp in milliseconds) |

***

#### Notes

* You can combine filters (e.g., `trader_id` and `datetime_from`) to narrow down results.
* The response will contain a list of `ManagerApiAccountBalanceOperation` entries.
* Timestamps must be in **Unix time in milliseconds**.

## Subscribe

This message is used by an authorized client to subscribe to real-time data streams (e.g., prices).\
Once subscribed, the client will receive updates automatically when the corresponding data changes.

#### JSON Structure

```json
{
  "message_id": "string",
  "message_response_id": null,
  "message_type": {
    "client_message": {
      "subscribe": {
        "topics": ["accounts", "orders", "prices"]
      }
    }
  }
}
```

#### Fields

| Field  | Type          | Description                              |
| ------ | ------------- | ---------------------------------------- |
| topics | `Arr<string>` | List of predefined subscriptions topics. |

#### Available `SubscriptionTopic`values for subscription

| Value                 | Description                                                                                                                                                            |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"prices"`            | Subscribe to real-time price updates.                                                                                                                                  |
| `"calculate_updates"` | Subscribe to position pl and account margin recalculations.                                                                                                            |
| `accounts`            | Subscribe to accounts general updates and get snapshot.                                                                                                                |
| `orders`              | Subscribe to orders general updates and get snapshot.                                                                                                                  |
| `positions`           | Subscribe to positions general updates and get snapshot.                                                                                                               |
| `trading_settings`    | Subscribe to settings general updates and get snapshot. Trading Groups, Trading Instruments, Trading Profiles, Markup Profiles, Dayoff Profiles, Collaterals |

* JSON string values must match snake\_case enum names.
* Deserialization is strict — invalid topics will result in error before request processing.
