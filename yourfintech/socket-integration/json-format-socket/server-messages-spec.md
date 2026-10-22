# Server messages spec

The `YftJsonIntegrationServerMessage` enum defines the different types of messages that the server can send to the client. Each message type corresponds to a specific information or event that the client needs to handle.

All keys and all enum values are **snake_case**, exactly as shown below.

Message types:

- `auth_result`
- `bid_ask`
- `account_event`
- `position`
- `pending_position`
- `accounts_recalculation_result`
- `positions_recalculation_result`

Field-by-field definitions live in [Root Models](root-models.md).

## auth_result

The `auth_result` message communicates the outcome of an authentication attempt by the client.

```json
{
  "message_id": "test",
  "message_type": {
    "server_message": {
      "auth_result": {
        "result": "success",
        "session_id": "f2b1c0d4-8e3a-4b6c-9d1e-0a2b3c4d5e6f"
      }
    }
  }
}
```

```json
{
  "message_id": "test",
  "message_type": {
    "server_message": {
      "auth_result": {
        "result": "auth_failed",
        "session_id": null
      }
    }
  }
}
```

```json
{
  "message_id": "test",
  "message_type": {
    "server_message": {
      "auth_result": {
        "result": "unauthorized",
        "session_id": null
      }
    }
  }
}
```

| Field | Description |
| --- | --- |
| result | Outcome. Enum: success; auth_failed; unauthorized; invalid_brand_config. |
| session_id | Session UUID when authentication succeeded, otherwise `null`. |

## bid_ask

The `bid_ask` message provides the latest bid and ask prices for a specific financial instrument.

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440001",
  "message_type": {
    "server_message": {
      "bid_ask": {
        "id": "BTCUSD",
        "bid": 50000,
        "ask": 50000.01,
        "date": 1704067200000
      }
    }
  }
}
```

| Field | Description |
| --- | --- |
| id | Unique identifier of the instrument (e.g. "BTCUSD"). |
| bid | Bid price, the price buyers are willing to pay. |
| ask | Ask price, the price sellers are asking. |
| date | Timestamp of the quote (Unix ms). |

## account_event

The `account_event` message notifies the client about events or changes related to an account.
It carries either `account_added` or `account_update`; `account_update` wraps the account
together with the balance operation that caused the change, when there was one.

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440000",
  "message_type": {
    "server_message": {
      "account_event": {
        "account_added": {
          "id": "123e4567-e89b-12d3-a456-426614174000",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "currency": "USD",
          "balance": 10000,
          "trading_disabled": false,
          "leverage": 10,
          "trading_group": "Main",
          "create_date": 1704067200000,
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000001",
          "metadata": {}
        }
      }
    }
  }
}
```

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440002",
  "message_type": {
    "server_message": {
      "account_event": {
        "account_update": {
          "account": {
            "id": "123e4567-e89b-12d3-a456-426614174000",
            "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
            "currency": "USD",
            "balance": 10100,
            "trading_disabled": false,
            "leverage": 10,
            "trading_group": "Main",
            "create_date": 1704067200000,
            "last_update_date": 1704067200000,
            "last_update_process_id": "demo-process-000001",
            "metadata": {}
          },
          "operation": {
            "id": "b1d2c3e4-5f60-4718-92a3-b4c5d6e7f809",
            "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
            "account_id": "123e4567-e89b-12d3-a456-426614174000",
            "operation_type": "deposit",
            "process_id": "demo-process-000002",
            "delta": 100,
            "date_time_unix_ms": 1704067200000,
            "comment": "Deposit",
            "reference_operation_id": null
          }
        }
      }
    }
  }
}
```

| Account field | Description |
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

## position

The `position` message informs the client about their positions. It carries `created`,
`updated` or `closed`, each wrapping a full position object.

**created** — a position was opened:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440010",
  "message_type": {
    "server_message": {
      "position": {
        "created": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "asset_pair": "BTCUSD",
          "is_buy": true,
          "create_date": 1704067200000,
          "tp_price": null,
          "sl_price": null,
          "create_process_id": "demo-process-000003",
          "profit": -0.1,
          "metadata": {},
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000003",
          "asset_open_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "open_date": 1704067200000,
          "open_process_id": "demo-process-000003",
          "close_date": null,
          "close_reason": null,
          "asset_close_bid_ask": null,
          "close_process_id": null,
          "collateral_currency": "USD",
          "margin_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "profit_bid_ask": {
            "id": "BTCUSD",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000
          },
          "swaps": [],
          "lots_amount": 0.01,
          "commission": 0.1,
          "is_trailing_sl": false,
          "realized_pl": null,
          "initial_lots_amount": 0.01
        }
      }
    }
  }
}
```

**updated** — the position changed. Below, a trailing stop loss has been switched on, so
`is_trailing_sl` is `true` and `sl_price` is the level the engine now moves on the
trader's behalf:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440012",
  "message_type": {
    "server_message": {
      "position": {
        "updated": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "asset_pair": "BTCUSD",
          "is_buy": true,
          "create_date": 1704067200000,
          "tp_price": null,
          "sl_price": 50250,
          "create_process_id": "demo-process-000003",
          "profit": -0.1,
          "metadata": {},
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000007",
          "asset_open_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "open_date": 1704067200000,
          "open_process_id": "demo-process-000003",
          "close_date": null,
          "close_reason": null,
          "asset_close_bid_ask": null,
          "close_process_id": null,
          "collateral_currency": "USD",
          "margin_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "profit_bid_ask": {
            "id": "BTCUSD",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000
          },
          "swaps": [],
          "lots_amount": 0.01,
          "commission": 0.1,
          "is_trailing_sl": true,
          "realized_pl": null,
          "initial_lots_amount": 0.01
        }
      }
    }
  }
}
```

**updated** — after a partial close. The position is still open and keeps its `id`;
`lots_amount` has dropped, `initial_lots_amount` is unchanged, and `realized_pl` holds
the result booked so far:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440013",
  "message_type": {
    "server_message": {
      "position": {
        "updated": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "asset_pair": "BTCUSD",
          "is_buy": true,
          "create_date": 1704067200000,
          "tp_price": null,
          "sl_price": null,
          "create_process_id": "demo-process-000003",
          "profit": -0.1,
          "metadata": {},
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000008",
          "asset_open_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "open_date": 1704067200000,
          "open_process_id": "demo-process-000003",
          "close_date": null,
          "close_reason": null,
          "asset_close_bid_ask": null,
          "close_process_id": null,
          "collateral_currency": "USD",
          "margin_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "profit_bid_ask": {
            "id": "BTCUSD",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000
          },
          "swaps": [],
          "lots_amount": 0.006,
          "commission": 0.1,
          "is_trailing_sl": false,
          "realized_pl": 1.84,
          "initial_lots_amount": 0.01
        }
      }
    }
  }
}
```

> A partial close arrives as `updated`, never as `closed`. A client that reconciles only
> on `closed` will miss the volume change. See
> [Position lifecycle features](../../position-lifecycle-features.md#partial-close).

**closed** — the position was closed in full:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440014",
  "message_type": {
    "server_message": {
      "position": {
        "closed": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "asset_pair": "BTCUSD",
          "is_buy": true,
          "create_date": 1704067200000,
          "tp_price": 50500,
          "sl_price": null,
          "create_process_id": "demo-process-000003",
          "profit": 4.99,
          "metadata": {},
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000003",
          "asset_open_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "open_date": 1704067200000,
          "open_process_id": "demo-process-000003",
          "close_date": 1704070800000,
          "close_reason": "take_profit",
          "asset_close_bid_ask": {
            "id": "BTCUSD",
            "bid": 50500,
            "ask": 50500.01,
            "date": 1704070800000
          },
          "close_process_id": "demo-process-000009",
          "collateral_currency": "USD",
          "margin_bid_ask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000
          },
          "profit_bid_ask": {
            "id": "BTCUSD",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000
          },
          "swaps": [],
          "lots_amount": 0.01,
          "commission": 0.1,
          "is_trailing_sl": false,
          "realized_pl": null,
          "initial_lots_amount": 0.01
        }
      }
    }
  }
}
```

| Field | Description |
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

## pending_position

The `pending_position` message informs the client about pending orders. It carries
`created`, `updated`, `failed`, `executed` or `canceled`.

**created**:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440020",
  "message_type": {
    "server_message": {
      "pending_position": {
        "created": {
          "id": "7f1c2d3e-4b5a-6789-0abc-def012345678",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "asset_pair": "BTCUSD",
          "lots_amount": 0.01,
          "is_buy": true,
          "create_date": 1704067200000,
          "tp_price": null,
          "sl_price": null,
          "create_process_id": "demo-process-000010",
          "metadata": {},
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000010",
          "collateral_currency": "USD",
          "desire_price": 49000,
          "order_type": "limit",
          "position_id": null,
          "is_trailing_sl": false
        }
      }
    }
  }
}
```

**executed** — the order produced a position, so `position_id` is now set:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440021",
  "message_type": {
    "server_message": {
      "pending_position": {
        "executed": {
          "id": "7f1c2d3e-4b5a-6789-0abc-def012345678",
          "trader_id": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "asset_pair": "BTCUSD",
          "lots_amount": 0.01,
          "is_buy": true,
          "create_date": 1704067200000,
          "tp_price": null,
          "sl_price": null,
          "create_process_id": "demo-process-000010",
          "metadata": {},
          "last_update_date": 1704067200000,
          "last_update_process_id": "demo-process-000011",
          "collateral_currency": "USD",
          "desire_price": 49000,
          "order_type": "limit",
          "position_id": "550e8400-e29b-41d4-a716-446655440011",
          "is_trailing_sl": false
        }
      }
    }
  }
}
```

`canceled`, `failed` and `updated` use the same shape.

| Field | Description |
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

## accounts_recalculation_result

Sent after a recalculation, carrying an array of per-account results.

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440030",
  "message_type": {
    "server_message": {
      "accounts_recalculation_result": [
        {
          "id": "123e4567-e89b-12d3-a456-426614174000",
          "margin": 408.14,
          "equity": 10100,
          "free_margin": 9691.86,
          "timestamp": 1704067200000
        }
      ]
    }
  }
}
```

| Field | Description |
| --- | --- |
| id | Identifier of the account. |
| margin | Recalculated margin. |
| equity | Recalculated equity. |
| free_margin | Recalculated free margin. |
| timestamp | When the recalculation was produced (Unix ms). |

## positions_recalculation_result

Sent after a recalculation, carrying an array of per-position results.

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440031",
  "message_type": {
    "server_message": {
      "positions_recalculation_result": [
        {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "account_id": "123e4567-e89b-12d3-a456-426614174000",
          "pl": -0.1,
          "timestamp": 1704067200000
        }
      ]
    }
  }
}
```

| Field | Description |
| --- | --- |
| id | Identifier of the position. |
| account_id | Identifier of the account the position belongs to. |
| pl | Recalculated profit or loss. |
| timestamp | When the recalculation was produced (Unix ms). |
