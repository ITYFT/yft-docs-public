# Server message spec

# Basic server message structure

## Description

Server messages are used to deliver:

- **Responses** to client requests (e.g. account data, authentication result)
- **Real-time updates** (e.g. price or position changes)
- **Snapshots** of full entity lists (e.g. all accounts or trades)
- **System errors** indicating problems with the request or processing

All messages are wrapped in the `server_message` field inside `message_type`.

There are two core categories of server messages:

- **Response messages** – contain either a `success` or `error` value and always include `message_response_id` linking them to the original client request
- **Event messages** – broadcast snapshots or updates with `snapshot` or `update` keys; these do **not** include `message_response_id`

Each payload is grouped by topic (e.g., `accounts`, `orders`, `positions`, etc.).

## Json Structure

### Event Message

Used for broadcasting real-time updates or snapshots to all or subscribed clients.

```json
{
  "messageid": "string",               // Unique message ID (UUID)
  "messageresponseid": null,         // Always null for event messages
  "messagetype": {
    "servermessage": {
      "positions": {
        "snapshot": [
          // Full array of domain objects (e.g. positions)
        ]
      }
    }
  }
}
```

Or for updates:

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "positions": {
        "update": {
          // Single object or array of updated domain objects
        }
      }
    }
  }
}
```

Or for errors

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "positions": {
        "error": "unauthorized"
      }
    }
  }
} 
```

### Response Messages

Used for responses to client-initiated requests (e.g., `get_positions`, `auth_request`, etc.).

```json
{
  "messageid": "string",
  "messageresponseid": "uuid", // Refers to original request messageid
  "messagetype": {
    "servermessage": {
      "getpositionsresponse": {
        "success": [
          // Array of positions
        ]
      }
    }
  }
}
```

**Error Message**

```json
{
  "messageid": "string",
  "messageresponseid": "uuid|null", // Present only if it's a response error
  "messagetype": {
    "servermessage": {
      "getpositionsresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

## Server Message Envelope Fields

| Field               | Type         | Description                                                                          |
|---------------------|--------------|--------------------------------------------------------------------------------------|
| message_id          | string       | Unique UUID of the outgoing server message                                           |
| message_response_id | string (opt) | UUID of the original client message (for responses)                                  |
| message_type        | object       | Contains the  server_message , which includes either a  response  or  event  payload |

---

## YftManagerApiServerMessage

### Description

`YftManagerApiServerMessage` is the main enum used by the server to send any outgoing message.

Each variant wraps a payload using one of the following wrappers:

1. `YftManagerApiServerResponse<T>`
   Used for **client request responses**, containing either:

- `Success(T)`
- `Error(YftManagerApiErrorType)`

1. `YftManagerApiEventType<T, F>`
   Used for **event streams**, containing either:

- `Snapshot(F)` — full list (e.g. all orders)
- `Update(T)` — new or changed item(s)

This design allows the client to clearly understand:

- If the message is a **reply** or an **event**
- Whether to **replace** or **merge** data
- How to handle errors contextually

### Enum: `YftManagerApiServerMessage`

| Variant name                | Description (EN)                                                 |
|-----------------------------|------------------------------------------------------------------|
| AuthResponse                | Response to  AuthRequest , wraps success or error                |
| ServerTimeResponse          | Response to  GetServerTime                                       |
| GetAccountsResponse         | Response to  GetAccounts , contains list of accounts             |
| GetPositionsResponse        | Response to  GetPositions , contains list of positions           |
| GetLastPriceResponse        | Response to  GetLastPrices , contains price list                 |
| UpdateBalanceResponse       | Response to  UpdateBalance , returns updated account             |
| GetHistoryPositionsResponse | Response to  GetHistoryPositions , contains historical positions |
| GetOrdersResponse           | Response to  GetOrders , returns order list                      |
| GetTradesResponse           | Response to  GetTrades , returns trade list                      |
| SubscribeResult             | Response to  Subscribe , indicates subscription success/failure  |
| Accounts                    | Event: snapshot or update of account data                        |
| Positions                   | Event: snapshot or update of position data                       |
| HistoryPositions            | Event: snapshot/update of historical positions                   |
| LastPrices                  | Event: snapshot or update of prices                              |
| Orders                      | Event: snapshot or update of orders                              |
| Trades                      | Event: snapshot or update of trades                              |
| TradingGroups               | Event: snapshot or update of trading groups                      |
| TradingInstruments          | Event: snapshot or update of instruments                         |
| TradingProfiles             | Event: snapshot or update of trading profiles                    |
| TradingMarkups              | Event: snapshot or update of markup profiles                     |
| DayOffProfiles              | Event: snapshot or update of day-off profiles                    |

## Error Codes

When an operation fails, the `error` field in the response will contain one of the following string values from the `YftManagerApiErrorType` enum.

| Error Code                                | Description                                                            |
|-------------------------------------------|------------------------------------------------------------------------|
| unauthorized                              | The client's request requires authentication.                          |
| auth_failed                               | Authentication failed due to an invalid secret key.                    |
| unknown_topic                             | The topic specified in a  subscribe  request does not exist.           |
| network_error                             | An internal network error occurred while processing the request.       |
| unexpected                                | An unexpected internal server error occurred.                          |
| invalid_message_format                    | The server could not parse the client's JSON message.                  |
| account_not_found                         | The specified account could not be found.                              |
| receiver_account_not_found                | The destination account for a transfer could not be found.             |
| day_off                                   | The requested operation cannot be performed due to a trading holiday.  |
| trading_group_not_found                   | The specified trading group does not exist.                            |
| asset_pair_trading_settings_not_found     | Trading settings for the specified asset pair are missing.             |
| not_enough_balance                        | The account has insufficient funds for the operation.                  |
| invalid_balance_transfer_amount           | The amount for a balance transfer is invalid (e.g., zero or negative). |
| asset_pair_price_not_found                | The price for the specified asset pair could not be found.             |
| commission_price_not_found                | Price required for commission calculation is missing.                  |
| margin_price_not_found                    | Price required for margin calculation is missing.                      |
| asset_pair_not_found                      | The specified asset pair does not exist.                               |
| profit_price_not_found                    | Price required for profit calculation is missing.                      |
| account_trading_disabled                  | Trading is disabled for the specified account.                         |
| no_liquidity                              | The order could not be executed due to a lack of liquidity.            |
| invalid_sl                                | The provided Stop Loss value is invalid.                               |
| invalid_tp                                | The provided Take Profit value is invalid.                             |
| position_not_found                        | The specified position could not be found.                             |
| invalid_order_partial_execution_quantity  | The quantity for a partial order execution is invalid.                 |
| trading_instrument_settings_not_found     | Settings for the specified trading instrument are missing.             |
| operation_not_support_for_this_order_type | The requested operation is not supported for this order type.          |
| order_not_found                           | The specified order could not be found.                                |
| invalid_collateral                        | The specified collateral is invalid.                                   |
| account_duplicate                         | An attempt was made to create an account that already exists.          |
| invalid_leverage                          | The provided leverage value is invalid.                                |
| collateral_not_found_in_group_settings    | The specified collateral is not configured for the trading group.      |
| trading_account_settings_not_found        | Settings for the specified trading account are missing.                |
| lots_too_low                              | The order's lot size is below the allowed minimum.                     |
| lots_too_high                             | The order's lot size is above the allowed maximum.                     |
| invalid_desire_price                      | The desired price for a pending order is invalid.                      |
| sender_account_disabled                   | The sending account in a transfer is disabled.                         |
| reciever_account_disabled                 | The receiving account in a transfer is disabled.                       |
| convert_id_error                          | An error occurred while converting a numeric ID to a UUID.             |
| sqlx_error                                | An internal database error occurred.                                   |

# AuthResponse

The `AuthResponse` message is sent in reply to an `AuthRequest`.
It indicates whether the authentication was successful and, if so, includes session and user details.

Depending on the result, the response may contain either:

- `success`: with a full `AuthResultJsonMessage` payload
- `error`: with a standardized error type from `YftManagerApiErrorType` (e.g., `unauthorized`, `auth_failed`)

**JSON Structure**

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "authresponse": {
        "success": {
          "sessionid": "string"
        }
      }
    }
  }
}
```

Field Reference

| Field               | Type           | Description                                     |
|---------------------|----------------|-------------------------------------------------|
| message_id          | string         | Unique ID of the outgoing server message        |
| message_response_id | string         | UUID of the original  AuthRequest               |
| message_type        | object         | Wraps  server_message.auth_response             |
| success.session_id  | string         | Session ID assigned to the authenticated client |
| error               | string  (enum) | Error code:  unauthorized ,  auth_failed , etc. |

# ServerTime

Returns the current server time.
Used by clients to synchronize clocks, measure latency, or test connectivity.

**JSON Structure**

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "servertimeresponse": {
        "success": {
          "servertime": "2025-06-15T19:38:17.000+03:00"
        }
      }
    }
  }
}
```

**Field Reference**

| Field               | Type   | Description                                                                        |
|---------------------|--------|------------------------------------------------------------------------------------|
| message_id          | string | Unique identifier of this server message.                                          |
| message_type        | string | Always  "server_message"  for server responses.                                    |
| message_response_id | string | Corresponds to the original client message this is replying to.                    |
| server_time         | string | Server time in ISO 8601 / RFC 3339 format, e.g.  "2025-06-05T19:38:17.000+03:00" . |

# GetAccountsResponse

Returns a list of trading accounts available to the authenticated user.
This message is a direct response to a `GetAccounts` request.

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getaccountsresponse": {
        "success": [
          {
            / ManagerApiAccount object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getaccountsresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

---

### 📑 Field Reference

| Field               | Type   | Description                                             |
|---------------------|--------|---------------------------------------------------------|
| message_id          | string | Unique identifier of this server message                |
| message_response_id | string | UUID of the original  GetAccounts  request              |
| success             | array  | List of account objects available to the user           |
| error               | string | Error code if the request failed ( unauthorized , etc.) |

---

📌 See the [ManagerApiAccount](root-models.md#managerapiaccount)model for details on the account structure.

# GetPositionsResponse

Returns a list of currently open trading positions for the authenticated user or account.
This message is a direct response to a `GetPositions` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getpositionsresponse": {
        "success": [
          {
            / ManagerApiPosition object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getpositionsresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

---

### 📑 Field Reference

| Field               | Type   | Description                                             |
|---------------------|--------|---------------------------------------------------------|
| message_id          | string | Unique identifier of this server message                |
| message_response_id | string | UUID of the original  GetPositions  request             |
| success             | array  | List of open positions                                  |
| error               | string | Error code if the request failed ( unauthorized , etc.) |

---

🔎 See the [ManagerApiPosition](root-models.md#managerapiposition) model for details on the position structure.

# ClosePositionResponse

### Description

The response to a `ClosePosition` request.
On success, returns the closed `ManagerApiOrder`.
On failure, returns a general error code.

### Success

- Returns [`ManagerApiOrder`](root-models.md#managerapiorder).

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "closepositionsresponse": {
        "success": [
          {
            / ManagerApiOrder object /
          }
        ]
      }
    }
  }
}
```

### Error

- Returns `Error`.

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "closepositionsresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

Possible error values:

- `network_error` – gRPC call failed (details logged).
- `unexpected` – No result returned (details logged).
- `unauthorized` -  Authentication failed or user does not have permission to perform action.

# UpdatePositionResponse

### Description

The response to an `UpdatePosition` request.
On success, returns the updated `ManagerApiPosition`.
On failure, returns a general error code.

### Success

- Returns [`ManagerApiPosition`](root-models.md#managerapiposition).

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "updatepositionsresponse": {
        "success": [
          {
            / ManagerApiPosition object /
          }
        ]
      }
    }
  }
}
```

### Error

- Returns `Error`.

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "updatepositionsresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

Possible error values:

- `network_error` – gRPC call failed (details logged).
- `unexpected` – No result returned (details logged).
- `unauthorized` -  Authentication failed or user does not have permission to perform action.

# GetHistoryPositionsResponse

Returns a list of historical (closed) trading positions for the authenticated user or account.
This message is a direct response to a `GetHistoryPositions` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "gethistorypositionsresponse": {
        "success": [
          {
            / ManagerApiPosition object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "gethistorypositionsresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                             |
|---------------------|--------|---------------------------------------------------------|
| message_id          | string | Unique identifier of this server message                |
| message_response_id | string | UUID of the original  GetHistoryPositions  request      |
| success             | array  | List of closed (historical) trading positions           |
| error               | string | Error code if the request failed ( unauthorized , etc.) |

---

See the [ManagerApiPosition](root-models.md#managerapiposition) model for details on the position structure.

# GetLastPriceResponse

Returns the latest bid/ask prices for all available trading instruments.
This message is a direct response to a `GetLastPrices` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getlastpriceresponse": {
        "success": [
          {
            / ManagerApiBidAsk object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getlastpriceresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                             |
|---------------------|--------|---------------------------------------------------------|
| message_id          | string | Unique identifier of this server message                |
| message_response_id | string | UUID of the original  GetLastPrices  request            |
| success             | array  | List of bid/ask price objects                           |
| error               | string | Error code if the request failed ( unauthorized , etc.) |

---

See the [ManagerApiBidAsk](root-models.md#managerapibidask) model for details on the bid/ask structure.

# UpdateBalanceResponse

> Returns updated account information **along with the balance operation** that caused the update (if any).
> This message is a direct response to an `UpdateBalance` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string (uuid)",
  "messageresponseid": "string (uuid)",
  "messagetype": {
    "servermessage": {
      "updatebalanceresponse": {
        "success": {
          "account": {
            "id": {
              "uuid": "string",
              "numid": "number (u64)"
            },
            "traderid": {
              "uuid": "string",
              "numid": "number (u64)"
            },
            "currency": "string",
            "balance": "number (f64)",
            "equity": "number (f64)",
            "margin": "number (f64)",
            "freemargin": "number (f64)",
            "marginlevel": "number (f64)",
            "leverage": "number (f64)",
            "tradinggroup": "string",
            "lastupdatedate": "number (i64)",
            "metadata": { "key": "value" },
            "status": "active" | "disabled",
            "hedgemode": "hedge" | "netting"
          },
          "balanceoperation": {
            "id": "string",
            "traderid": {
              "uuid": "string",
              "numid": "number (u64)"
            },
            "accountid": {
              "uuid": "string",
              "numid": "number (u64)"
            },
            "reason": "string (enum)",
            "processid": "string" | null,
            "delta": "number (f64)",
            "date": "number (i64)",
            "comment": "string" | null,
            "referenceoperationid": "string" | null
          } | null
        }
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "updatebalanceresponse": {
        "error": "networkerror"
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type          | Description                                                                                  |
|---------------------|---------------|----------------------------------------------------------------------------------------------|
| message_id          | string        | Unique identifier of this server message                                                     |
| message_response_id | string        | UUID of the original  UpdateBalance  request                                                 |
| success.updated     | object        | Updated account object (see  ManagerApiAccount ).                                            |
| success.operation   | object | null | Balance operation that triggered the update (see  ManagerApiAccountBalanceUpdateOperation ). |
| error               | string        | Error code if the request failed ( network_error , etc.)                                     |

---

See the [ManagerApiAccount](root-models.md#managerapiaccount) and [ManagerApiAccountBalanceUpdateOperation](root-models.md#managerapiaccountbalanceupdateoperation)model for details on the account structure.

# GetOrdersResponse

Returns a list of active orders for the authenticated user or account.
This message is a direct response to a `GetOrders` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getordersresponse": {
        "success": [
          {
            / ManagerApiOrder object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "getordersresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

### Field Reference

| Field               | Type   | Description                                             |
|---------------------|--------|---------------------------------------------------------|
| message_id          | string | Unique identifier of this server message                |
| message_response_id | string | UUID of the original  GetOrders  request                |
| success             | array  | List of active order objects                            |
| error               | string | Error code if the request failed ( unauthorized , etc.) |

---

See the [ManagerApiOrder](root-models.md#managerapiorder) model for details on the order structure.

# PlaceOrderResponse

### Description

The response to a `PlaceOrder` request.
On success, returns a `ManagerApiOrder` object.
On failure, returns a general error code: either `network_error` or `unexpected`.

### Success

- Returns [ManagerApiOrder](root-models.md#managerapiorder).

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "placeordersresponse": {
        "success": [
          {
            / ManagerApiOrder object /
          }
        ]
      }
    }
  }
}
```

### Error

- Returns `Error`.

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "placeordersresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

Possible error values:

- `network_error` – gRPC call failed (exact reason is logged).
- `unexpected` – No result returned by the trading engine (also logged).
- `unauthorized` -  Authentication failed or user does not have permission to perform action.

# CancelOrderResponse

### Description

The response to a `CancelOrder` request.
On success, returns the cancelled `ManagerApiOrder`.
On failure, returns a general error code.

### Success

- Returns [`ManagerApiOrder`](root-models.md#managerapiorder).

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "cancelordersresponse": {
        "success": [
          {
            / ManagerApiOrder object /
          }
        ]
      }
    }
  }
}
```

### Error

- Returns `Error`.

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "cancelordersresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

Possible error values:

- `network_error` – gRPC call failed (details logged internally).
- `unexpected` – No result returned (details logged).
- `unauthorized` -  Authentication failed or user does not have permission to perform action.

# UpdateOrderResponse

### Description

The response to an `UpdateOrder` request.
On success, returns the updated `ManagerApiOrder`.
On failure, returns a general error code.

### Success

- Returns [`ManagerApiOrder`](root-models.md#managerapiorder).

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "cancelordersresponse": {
        "success": [
          {
            / UpdateOrderResponse object /
          }
        ]
      }
    }
  }
}
```

### Error

- Returns `Error`.

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "updateordersresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

Possible error values:

- `network_error` – gRPC call failed (details logged).
- `unexpected` – No result returned (details logged).
- `unauthorized` -  Authentication failed or user does not have permission to perform action.

# GetTradesResponse

Returns a list of trades executed for the authenticated user or account.
This message is a direct response to a `GetTrades` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "gettradesresponse": {
        "success": [
          {
            / ManagerApiTrade object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "gettradesresponse": {
        "error": "unauthorized"
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                             |
|---------------------|--------|---------------------------------------------------------|
| message_id          | string | Unique identifier of this server message                |
| message_response_id | string | UUID of the original  GetTrades  request                |
| success             | array  | List of executed trade objects                          |
| error               | string | Error code if the request failed ( unauthorized , etc.) |

---

See the [ManagerApiTrade](root-models.md#managerapitrade) model for details on the trade structure.

# GetCollateralsResponse

This response returns a list of available collaterals in the system. These collaterals can be used for margin, balance calculations, or asset representation. Each item contains the identifier, name, and decimal precision of the asset.

### JSON Structure

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "getcollateralsresponse": {
      "success": [
        {
          "id": "string",
          "name": "string",
          "digits": 0
        }
      ]
    }
  }
}
```

---

### Field Description

| Field  | Type   | Description                                                               |
|--------|--------|---------------------------------------------------------------------------|
| id     | string | Unique identifier of the collateral (e.g.,  "USD" ,  "BTC" ).             |
| name   | string | Human-readable name of the collateral (e.g.,  "US Dollar" ).              |
| digits | number | Number of decimal places supported for the collateral (e.g.,  2  or  8 ). |

See the [ManagerApiCollateral](root-models.md#managerapicollateral) model for details on the collateral structure.

# GetBalanceOperationsResponse

This response provides a list of historical balance operations that have modified account balances — including deposits, withdrawals, trading-related updates, internal transfers, and corrections.

Each entry includes details such as the reason for the update, delta amount, and metadata for tracking and auditing.

---

### JSON Structure

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "getbalanceoperationsresponse": {
      "success": [
        {
          "id": {
            "uuid": "string",
            "numid": "number (u64)"
          },
          "traderid": {
            "uuid": "string",
            "numid": "number (u64)"
          },
          "accountid": {
            "uuid": "string",
            "numid": "number (u64)"
          },
          "reason": "deposit",
          "processid": "string",
          "delta": 100.0,
          "date": 1719950000000,
          "comment": "string (optional)",
          "referenceoperationid": {
    	    "uuid": "string",
    	    "numid": "number (u64)"
          } (optional),
        }
      ]
    }
  }
}
```

---

### Field Description

| Field                  | Type                              | Description                                                                                                                              |
|------------------------|-----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| id                     | UUID or\and Numeric ID            | Unique identifier of the balance operation                                                                                               |
| trader_id              | UUID or\and Numeric ID            | ID of the trader who owns the affected account                                                                                           |
| account_id             | UUID or\and Numeric ID            | ID of the account impacted by the operation                                                                                              |
| reason                 | string                            | Reason for the operation, one of:
 trading ,  deposit ,  withdrawal ,  transfer ,  balance_correction ,  commission ,  withdrawal_cancel |
| process_id             | string                            | Identifier for the related processing batch or system event                                                                              |
| delta                  | number                            | Amount added or subtracted (positive or negative)                                                                                        |
| date                   | number                            | Timestamp in milliseconds since the UNIX epoch                                                                                           |
| comment                | string (optional)                 | Optional comment or metadata                                                                                                             |
| reference_operation_id | UUID or\and Numeric ID (optional) | ID of a related operation, if this is a follow-up                                                                                        |

See the [ManagerApiAccountBalanceOperation](root-models.md#managerapiaccountbalanceupdateoperation)model for details on the collateral structure

# SubscribeResult

Indicates whether a client successfully subscribed to a specific data topic.
This message is a direct response to a `Subscribe` request.

---

### JSON Structure (Success)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "subscriberesult": {
        "success": true
      }
    }
  }
}
```

---

### JSON Structure (Error)

```json
{
  "messageid": "string",
  "messageresponseid": "string",
  "messagetype": {
    "servermessage": {
      "subscriberesult": {
        "error": "unknowntopic"
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type    | Description                                                              |
|---------------------|---------|--------------------------------------------------------------------------|
| message_id          | string  | Unique identifier of this server message                                 |
| message_response_id | string  | UUID of the original  Subscribe  request                                 |
| success             | boolean | true  if the subscription was successful                                 |
| error               | string  | Error code if the request failed ( unknown_topic ,  unauthorized , etc.) |

# Calculation updates

Every ~200 milliseconds, the system performs an internal check to detect any changes in calculated values for trading accounts or open positions. These recalculations are typically triggered by price updates, trading activity, or margin shifts.

When differences are found, compact update messages are sent to all subscribed clients via the `SubscriptionTopic::CalculateUpdates` channel.

These updates do **not** include full entities — only the fields that were recalculated.

---

### Account Calculation Updates

Delivered via:
`YftManagerApiMessage` → `message_type::server_message::accounts` → `update::calculate_updates`

This message includes a list of account updates, each containing:

- `account_id` – the affected account
- `margin` – current used margin
- `equity` – current equity
- `free_margin` – available free margin
- `margin_level` – margin level (%)

**JSON structure:**

```json
{
  "messageid": "string",
  "messageresponseid": "string|null",
  "messagetype": {
    "servermessage": {
      "accounts": {
        "update": {
          "calculateupdates": [
            {
              "accountid": {
                "uuid": "string",
                "numid": "number (u64)"
              },
              "margin": 0.0,
              "equity": 0.0,
              "freemargin": 0.0,
              "marginlevel": 0.0
            }
          ]
        }
      }
    }
  }
}
```

---

### Position Calculation Updates

Delivered via:
`YftManagerApiMessage` → `message_type::server_message::positions` → `update::calculate_updates`

This message contains a list of position updates, with:

- `account_id` – the account the position belongs to
- `position_id` – the updated position
- `gross_pl` – updated gross profit/loss

**JSON structure:**

```json
{
  "messageid": "string",
  "messageresponseid": "string|null",
  "messagetype": {
    "servermessage": {
      "positions": {
        "update": {
          "calculateupdates": [
            {
              "accountid": {
                "uuid": "string",
                "numid": "number (u64)"
              },
              "positionid": {
                "uuid": "string",
                "numid": "number (u64)"
              },
              "grosspl": 0.0
            }
          ]
        }
      }
    }
  }
}
```

---

These messages are lightweight, fast, and ideal for syncing just the essential real-time values with UI or monitoring systems.

# Accounts

Broadcast message that delivers either the full list of accounts or incremental updates.
This is an **event-type** message and is not tied to a specific request (i.e., `message_response_id` is always null).

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `accounts` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of current accounts).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of current accounts (`Vec<ManagerApiAccount>`)
- `update`: a single account-related change (`ManagerApiAccountEvent`)

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "accounts": {
        "snapshot": [
          {
            / ManagerApiAccount object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update – Added)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "accounts": {
        "update": {
          "added": {
            / ManagerApiAccount object /
          }
        }
      }
    }
  }
}
```

---

### JSON Structure (Update – Updated)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "accounts": {
        "update": {
          "updated": [
            / ManagerApiAccountBalanceUpdate objects /
          ]
        }
      }
    }
  }
}
```

Or, when no balance operation is present:

```json
"updated": [
  {
    / ManagerApiAccount object /
  },
  null
]
```

---

### Field Reference

| Field               | Type   | Description                                          |
|---------------------|--------|------------------------------------------------------|
| message_id          | string | Unique identifier of this server message             |
| message_response_id | null   | Always null for event messages                       |
| snapshot            | array  | Full list of accounts ( ManagerApiAccount )          |
| update              | object | A single change described by  ManagerApiAccountEvent |

---

### ManagerApiAccountEvent

Describes the nature of an account change:

| Variant           | Description                                                                                                                                                                                          |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| added             | A new account was created. Contains a  ManagerApiAccount  object                                                                                                                                     |
| updated           | An existing account was modified. Contains a  ManagerApiAccount  and an optional  ManagerApiAccountBalanceUpdateOperation  (e.g., deposit, withdrawal, correction)                                   |
| calculate_updates | Contains updated margin-related values for multiple accounts. Holds a  Vec<ManagerApiAccountCalculationUpdate>  with recalculated fields like  margin ,  equity ,  free_margin , and  margin_level . |

---

See the [ManagerApiAccount](root-models.md#managerapiaccount), [ManagerApiAccountBalanceUpdateOperation](root-models.md#managerapiaccountbalanceupdateoperation), [ManagerApiAccountBalanceUpdate](root-models.md#managerapiaccountbalanceupdate), [ManagerApiAccountCalculationUpdate](root-models.md#managerapiaccountcalculationupdate)models for structure details.

# Positions

Broadcast message that delivers either the full list of currently open positions or an incremental position update.
This is an **event-type** message and does **not** contain `message_response_id`.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `positions` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of active positions).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: list of all currently open positions (`Vec<ManagerApiPosition>`)
- `update`: a single position-related change (`ManagerApiPositionEvent`)

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "positions": {
        "snapshot": [
          {
            / ManagerApiPosition object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update – Created)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "positions": {
        "update": {
          "created": {
            / ManagerApiPosition object /
          }
        }
      }
    }
  }
}
```

---

### JSON Structure (Update – Updated)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "positions": {
        "update": {
          "updated": {
            / ManagerApiPosition object /
          }
        }
      }
    }
  }
}
```

---

### JSON Structure (Update – Closed)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "positions": {
        "update": {
          "closed": {
            / ManagerApiPosition object /
          }
        }
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                           |
|---------------------|--------|-------------------------------------------------------|
| message_id          | string | Unique identifier of this server message              |
| message_response_id | null   | Always null for event messages                        |
| snapshot            | array  | Full list of open positions ( ManagerApiPosition )    |
| update              | object | A single change described by  ManagerApiPositionEvent |

---

### ManagerApiPositionEvent

Describes the nature of a position change:

| Variant           | Description                                                                                                                                              |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| created           | A new position was opened                                                                                                                                |
| updated           | An existing position was updated (e.g. SL/TP, volume, etc.)                                                                                              |
| closed            | The position was closed                                                                                                                                  |
| calculate_updates | Contains updated profit/loss values for multiple positions. Holds a  Vec<ManagerApiPositionCalculationUpdate>  with recalculated fields like  gross_pl . |

Each variant contains a full `ManagerApiPosition` object.

---

See the [ManagerApiPosition](root-models.md#managerapiposition)  model for structure details.

# HistoryPositions

Broadcast message that delivers either a full snapshot or an update of closed (historical) trading positions.
This is an **event-type** message and is not tied to a specific client request.

The payload may contain:

- `snapshot`: full list of historical positions (`Vec<ManagerApiPosition>`)
- `update`: partial update (subset of closed positions, typically a `Vec<ManagerApiPosition>` as well)

> ⚠️ Unlike live positions, there is no separate event enum here — both `snapshot` and `update` use plain array of`ManagerApiPosition`.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "historypositions": {
        "snapshot": [
          {
            / ManagerApiPosition object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "historypositions": {
        "update": [
          {
            / ManagerApiPosition object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                              |
|---------------------|--------|------------------------------------------|
| message_id          | string | Unique identifier of this server message |
| message_response_id | null   | Always null for event messages           |
| snapshot            | array  | Full list of historical positions        |
| update              | array  | Subset of new/updated closed positions   |

---

See the [ManagerApiPosition](root-models.md#managerapiposition) model for details on the position structure.

# LastPrices

Broadcast message that delivers either a full snapshot or an update of the latest bid/ask prices.
This is an **event-type** message used for real-time price feeds.

The payload may contain:

- `snapshot`: full list of the latest prices for all instruments
- `update`: subset of instruments whose prices have changed

Both `snapshot` and `update` contain arrays of `ManagerApiBidAsk` objects.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "lastprices": {
        "snapshot": [
          {
            / ManagerApiBidAsk object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "lastprices": {
        "update": [
          {
            / ManagerApiBidAsk object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                     |
|---------------------|--------|-------------------------------------------------|
| message_id          | string | Unique identifier of this server message        |
| message_response_id | null   | Always null for event messages                  |
| snapshot            | array  | Latest known prices for all trading instruments |
| update              | array  | Price changes for one or more instruments       |

---

See the [ManagerBidAsk](root-models.md#managerapibidask)model for details on the price structure.

# Orders

Broadcast message that delivers either the full list of currently active orders or an incremental order change.
This is an **event-type** message and is not tied to a specific client request.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `orders` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of active orders).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of currently active orders
- `update`: a single order-related event (create, cancel, execute, etc.)

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "orders": {
        "snapshot": [
          {
            / ManagerApiOrder object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update – Examples)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "orders": {
        "update": {
          "created": {
            / ManagerApiOrder object /
          }
        }
      }
    }
  }
}
```

```json
"update": {
  "canceled": {
    / ManagerApiOrder object /
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                           |
|---------------------|--------|-------------------------------------------------------|
| message_id          | string | Unique identifier of this server message              |
| message_response_id | null   | Always null for event messages                        |
| snapshot            | array  | Full list of active orders                            |
| update              | object | A single order-related event ( ManagerApiOrderEvent ) |

---

### ManagerApiOrderEvent

Represents a change to a single order. The event can be one of the following:

| Variant  | Description                                 |
|----------|---------------------------------------------|
| created  | A new order was placed                      |
| updated  | An existing order was modified              |
| canceled | An order was canceled                       |
| executed | An order was fully or partially filled      |
| failed   | The order was rejected or failed to execute |

Each variant contains a full `ManagerApiOrder` object.

---

See the [ManagerApiOrder](root-models.md#managerapiorder) model for details on the order structure.

# TradingGroups

Broadcast message that delivers either the full list of trading groups or an incremental update.
This is an **event-type** message and is not tied to a specific request.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `trading_settings` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of trading group).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of available trading groups
- `update`: subset of added or changed trading groups

Both `snapshot` and `update` are arrays of `ManagerApiTradingGroup` objects.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradinggroups": {
        "snapshot": [
          {
            / ManagerApiTradingGroup object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradinggroups": {
        "update": [
          {
            / ManagerApiTradingGroup object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                              |
|---------------------|--------|------------------------------------------|
| message_id          | string | Unique identifier of this server message |
| message_response_id | null   | Always null for event messages           |
| snapshot            | array  | Full list of trading groups              |
| update              | array  | Newly added or changed trading groups    |

---

See the [ManagerApiTradingGroup](root-models.md#managerapitradinggroup) model for details on the trading group structure.

# TradingInstruments

Broadcast message that delivers either the full list of trading instruments or an incremental update.
This is an **event-type** message and is not tied to a specific request.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `trading_settings` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of trading instruments).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of available instruments
- `update`: subset of newly added or modified instruments

Both `snapshot` and `update` are arrays of `ManagerApiTradingInstrument` objects.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradinginstruments": {
        "snapshot": [
          {
            / ManagerApiTradingInstrument object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradinginstruments": {
        "update": [
          {
            / ManagerApiTradingInstrument object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                                |
|---------------------|--------|--------------------------------------------|
| message_id          | string | Unique identifier of this server message   |
| message_response_id | null   | Always null for event messages             |
| snapshot            | array  | Full list of available trading instruments |
| update              | array  | Newly added or updated trading instruments |

---

See the [ManagerApiTradingInstrument](root-models.md#managerapitradinginstrument) model for details on the instrument structure.

# TradingProfiles

Broadcast message that delivers either the full list of trading profiles or an incremental update.
This is an **event-type** message and is not tied to a specific client request.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `trading_settings` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of trading profiles).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of trading profiles
- `update`: subset of newly added or modified profiles

Both `snapshot` and `update` are arrays of `ManagerApiTradingProfile` objects.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradingprofiles": {
        "snapshot": [
          {
            / ManagerApiTradingProfile object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradingprofiles": {
        "update": [
          {
            / ManagerApiTradingProfile object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                              |
|---------------------|--------|------------------------------------------|
| message_id          | string | Unique identifier of this server message |
| message_response_id | null   | Always null for event messages           |
| snapshot            | array  | Full list of trading profiles            |
| update              | array  | Newly added or updated trading profiles  |

---

See the [ManagerApiTradingProfile](root-models.md#managerapitradingprofile) model for details on the profile structure.

# TradingMarkups

Broadcast message that delivers either the full list of trading markup profiles or an incremental update.
This is an **event-type** message and is not tied to any client request.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `trading_settings` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of trading markaps).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of markup profiles
- `update`: subset of newly added or modified markup profiles

Both `snapshot` and `update` are arrays of `ManagerApiMarkupProfile` objects.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradingmarkups": {
        "snapshot": [
          {
            / ManagerApiMarkupProfile object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "tradingmarkups": {
        "update": [
          {
            / ManagerApiMarkupProfile object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                              |
|---------------------|--------|------------------------------------------|
| message_id          | string | Unique identifier of this server message |
| message_response_id | null   | Always null for event messages           |
| snapshot            | array  | Full list of markup profiles             |
| update              | array  | Newly added or updated markup profiles   |

---

See the [ManagerApiMarkupProfile](root-models.md#managerapimarkupprofile) model for details on the markup structure.

# DayOffProfiles

Broadcast message that delivers either the full list of trading day-off profiles or an incremental update.
This is an **event-type** message and is not tied to any specific request.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `trading_settings` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of day of profiles).
- Further changes are delivered as **update** events.

The payload may contain:

- `snapshot`: full list of day-off profiles
- `update`: subset of newly added or modified profiles

Both `snapshot` and `update` are arrays of `ManagerApiDayOffProfile` objects.

---

### JSON Structure (Snapshot)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "dayoffprofiles": {
        "snapshot": [
          {
            / ManagerApiDayOffProfile object /
          }
        ]
      }
    }
  }
}
```

---

### JSON Structure (Update)

```json
{
  "messageid": "string",
  "messageresponseid": null,
  "messagetype": {
    "servermessage": {
      "dayoffprofiles": {
        "update": [
          {
            / ManagerApiDayOffProfile object /
          }
        ]
      }
    }
  }
}
```

---

### Field Reference

| Field               | Type   | Description                              |
|---------------------|--------|------------------------------------------|
| message_id          | string | Unique identifier of this server message |
| message_response_id | null   | Always null for event messages           |
| snapshot            | array  | Full list of trading day-off profiles    |
| update              | array  | Newly added or updated day-off profiles  |

---

See the [ManagerApiDayOffProfile](root-models.md#managerapidayoffprofile) model for details on the profile structure.

# Collateral

This message delivers **collateral snapshot or update events** from the server to the client. It reflects changes or initial state of available collateral assets used in margin trading, account balances, and valuation.

Subscription behavior

- To start receiving account events, the client must **subscribe** to the `trading_settings` topic.
- After a successful subscription, the server **immediately pushes a snapshot** (full list of collateral).
- Further changes are delivered as **update** events.

The event type can be either:

- `snapshot` – full current state of all available collaterals
- `update` – changes to the existing list (e.g., new collateral added, updated precision or name)

---

### JSON Structure

```json
{
  "messageid": "string",
  "messagetype": {
    "servermessage": {
      "collateral": {
        "snapshot": [
          {
            "id": "string",
            "name": "string",
            "digits": 0
          }
        ]
      }
    }
  }
}
```

Or:

```json
{
  "messageid": "string",
  "messagetype": {
    "servermessage": {
      "collateral": {
        "update": [
          {
            "id": "string",
            "name": "string",
            "digits": 0
          }
        ]
      }
    }
  }
}
```

---

### Field Description

| Field  | Type   | Description                                                          |
|--------|--------|----------------------------------------------------------------------|
| id     | string | Unique identifier of the collateral asset (e.g.,  "USD" ,  "ETH" )   |
| name   | string | Human-readable name of the asset (e.g.,  "US Dollar" ,  "Ethereum" ) |
| digits | number | Number of decimal places supported for this asset (e.g.,  2 ,  8 )   |

See the [ManagerApiCollateral](root-models.md#managerapicollateral) model for details on the collateral structure.