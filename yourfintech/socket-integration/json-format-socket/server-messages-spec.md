# Server messages spec

The `YftJsonIntegrationServerMessage` enum defines the different types of messages that the server can send to the client. Each message type corresponds to a specific information or event that the client needs to handle.

Message Types:

- AuthResult
- BidAsk
- AccountEvent
- Position
- PendingPosition

## AuthResult

The `AuthResult` message communicates the outcome of an authentication attempt by the client.

```json
{
  "messageid": "test",
  "messagetype": {
    "servermessage": {
      "authresult": {
        "result": "authfailed",
        "sessionid": null
      }
    }
  }
}

{
  "messageid": "test",
  "messagetype": {
    "servermessage": {
      "authresult": {
        "result": "success",
        "sessionid": "id"
      }
    }
  }
}

{
  "messageid": "test",
  "messagetype": {
    "servermessage": {
      "authresult": {
        "result": "unauthorized",
        "sessionid": null
      }
    }
  }
}

```

| Name       | Type                                                             | Desc            |
|------------|------------------------------------------------------------------|-----------------|
| Result     | [String] Enum: Success;  AuthFailed - tech issues; Unauthorized; | Result status   |
| Session Id | String (Optional)                                                | Uuid of session |

## BidAsk

The `BidAsk` message provides the latest bid and ask prices for a specific financial instrument.

```json
{
  "messageid": "test",
  "messagetype": {
    "servermessage": {
      "bidask": //bidask
    }
  }
}
```

| Name    | Type   | Description |
|---------|--------|-------------|
| bid_ask | BidAsk | BidAskModel |

## 

## AccountEvent

The `AccountEvent` message notifies the client about events or changes related to their account.

```json
{
  "messageid": "550e8400-e29b-41d4-a716-446655440000",
  "messagetype": {
    "servermessage": {
      "accountevent": {
        "accountupdate": {
          "account": {
            "id": "123e4567-e89b-12d3-a456-426614174000",
            "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
            "currency": "USD",
            "balance": 10000.00,
            "tradingdisabled": false,
            "leverage": 10,
            "tradinggroup": "Main",
            "stopout": 100,
            "createdate": 1704067200000000,
            "lastupdatedate": 1704067200000001,
            "lastupdateprocessid": "demo-process-000001",
            "metadata": {}
          },
          "operation": {
            "id": "550e8400-e29b-41d4-a716-446655440001",
            "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
            "accountid": "123e4567-e89b-12d3-a456-426614174000",
            "operationtype": "Trading",
            "processid": "demo-process-000002",
            "delta": -0.50,
            "datetimeunixms": 1704067200000002,
            "comment": "Trading result",
            "referenceoperationid": "550e8400-e29b-41d4-a716-446655440002"
          }
        }
      }
    }
  }
}

{
  "messageid": "550e8400-e29b-41d4-a716-446655440000",
  "messagetype": {
    "servermessage": {
      "accountevent": {
        "accountadded": {
            "id": "123e4567-e89b-12d3-a456-426614174000",
            "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
            "currency": "USD",
            "balance": 0.0,
            "tradingdisabled": false,
            "leverage": 10,
            "tradinggroup": "Main",
            "stopout": 100,
            "createdate": 1704067200000000,
            "lastupdatedate": 1704067200000001,
            "lastupdateprocessid": "demo-process-000001",
            "metadata": {}
          }
      }
    }
  }
}
```

Account Event is an enum, so you have 2 possible options there;

| Name           | Type          | Description                 |
|----------------|---------------|-----------------------------|
| Account Added  | Account       | New account added to system |
| AccountUpdated | AccountUpdate | Exsisted account updated    |

### AccountUpdate

| Name              | Type                         | Description                 |
|-------------------|------------------------------|-----------------------------|
| Account           | Account                      | Account model after update  |
| Balance Operation | BalanceOpeartion  (Optional) | Only if balance was updated |

## Position event

The `Position` message informs the client about their trading positions, including opening, updating, or closing positions.

```json
{
  "messageid": "550e8400-e29b-41d4-a716-446655440010",
  "messagetype": {
    "servermessage": {
      "position": {
        "created": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "accountid": "123e4567-e89b-12d3-a456-426614174000",
          "assetpair": "BTCUSD",
          "lostamount": 0.01,
          "side": "Buy",
          "leverage": 0,
          "createdate": 1704067200000003,
          "tpininstrumentprice": null,
          "tpincurrency": null,
          "slininstrumentprice": null,
          "slincurrency": null,
          "createprocessid": "demo-process-000003",
          "profit": -0.10,
          "metadata": {},
          "lastupdatedate": 1704067200000005,
          "lastupdateprocessid": "demo-process-000003",
          "assetopenprice": 50000.01,
          "assetopenbidask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000004,
            "base": "BTC",
            "quote": "USD"
          },
          "opendate": 1704067200000007,
          "openprocessid": "demo-process-000003",
          "closedate": null,
          "closereason": null,
          "assetcloseprice": null,
          "assetclosebidask": null,
          "closeprocessid": null,
          "base": "BTC",
          "quote": "USD",
          "collateralcurrency": "USD",
          "marginprice": 50000.01,
          "marginbidask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000004,
            "base": "BTC",
            "quote": "USD"
          },
          "profitprice": 1,
          "profitbidask": {
            "id": "",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000006,
            "base": "",
            "quote": ""
          },
          "swaps": [],
          "lotsamount": 0.01,
          "lotssize": 1,
          "commission": 0.10
        }
      }
    }
  }
}

{
  "messageid": "550e8400-e29b-41d4-a716-446655440010",
  "messagetype": {
    "servermessage": {
      "position": {
        "updated": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "accountid": "123e4567-e89b-12d3-a456-426614174000",
          "assetpair": "BTCUSD",
          "lostamount": 0.01,
          "side": "Buy",
          "leverage": 0,
          "createdate": 1704067200000003,
          "tpininstrumentprice": 25.0,
          "tpincurrency": null,
          "slininstrumentprice": null,
          "slincurrency": null,
          "createprocessid": "demo-process-000003",
          "profit": -0.10,
          "metadata": {},
          "lastupdatedate": 1704067200000005,
          "lastupdateprocessid": "demo-process-000003",
          "assetopenprice": 50000.01,
          "assetopenbidask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000004,
            "base": "BTC",
            "quote": "USD"
          },
          "opendate": 1704067200000007,
          "openprocessid": "demo-process-000003",
          "closedate": null,
          "closereason": null,
          "assetcloseprice": null,
          "assetclosebidask": null,
          "closeprocessid": null,
          "base": "BTC",
          "quote": "USD",
          "collateralcurrency": "USD",
          "marginprice": 50000.01,
          "marginbidask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000004,
            "base": "BTC",
            "quote": "USD"
          },
          "profitprice": 1,
          "profitbidask": {
            "id": "",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000006,
            "base": "",
            "quote": ""
          },
          "swaps": [],
          "lotsamount": 0.01,
          "lotssize": 1,
          "commission": 0.10
        }
      }
    }
  }
}

{
  "messageid": "550e8400-e29b-41d4-a716-446655440030",
  "messagetype": {
    "servermessage": {
      "position": {
        "closed": {
          "id": "550e8400-e29b-41d4-a716-446655440011",
          "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "accountid": "123e4567-e89b-12d3-a456-426614174000",
          "assetpair": "BTCUSD",
          "lostamount": 0.01,
          "side": "Buy",
          "leverage": 0,
          "createdate": 1704067200000003,
          "tpininstrumentprice": 100000,
          "tpincurrency": null,
          "slininstrumentprice": null,
          "slincurrency": null,
          "createprocessid": "demo-process-000003",
          "profit": -1.25,
          "metadata": {},
          "lastupdatedate": 1704067200000008,
          "lastupdateprocessid": "demo-process-000004",
          "assetopenprice": 50000.01,
          "assetopenbidask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000004,
            "base": "BTC",
            "quote": "USD"
          },
          "opendate": 1704067200000007,
          "openprocessid": "demo-process-000003",
          "closedate": null,
          "closereason": null,
          "assetcloseprice": null,
          "assetclosebidask": null,
          "closeprocessid": null,
          "base": "BTC",
          "quote": "USD",
          "collateralcurrency": "USD",
          "marginprice": 50000.01,
          "marginbidask": {
            "id": "BTCUSD",
            "bid": 50000,
            "ask": 50000.01,
            "date": 1704067200000004,
            "base": "BTC",
            "quote": "USD"
          },
          "profitprice": 1,
          "profitbidask": {
            "id": "",
            "bid": 1,
            "ask": 1,
            "date": 1704067200000006,
            "base": "",
            "quote": ""
          },
          "swaps": [],
          "lotsamount": 0.01,
          "lotssize": 1,
          "commission": 0.10
        }
      }
    }
  }
}
```

Position Event is an enum, so you have 3 possible options there;

| Name    | Type     | Description      |
|---------|----------|------------------|
| Created | Position | Position created |
| Updated | Position | Position updated |
| Closed  | Position | Position closed  |

## Pending position

The `PendingPosition` message notifies the client about positions that are pending execution, such as limit or stop orders.

```json
{
  "messageid": "550e8400-e29b-41d4-a716-446655440020",
  "messagetype": {
    "servermessage": {
      "pendingposition": {
        "created": {
          "id": "550e8400-e29b-41d4-a716-446655440021",
          "traderid": "987fcdeb-51a2-43d1-ba16-614b5a7f3200",
          "accountid": "123e4567-e89b-12d3-a456-426614174000",
          "assetpair": "BTCUSD",
          "lotsamount": 0.01,
          "side": "Sell",
          "createdate": 1704067200000009,
          "tpininstrumentprice": null,
          "tpincurrency": null,
          "slininstrumentprice": null,
          "slincurrency": null,
          "createprocessid": "demo-process-000005",
          "metadata": {},
          "lastupdatedate": 1704067200000010,
          "lastupdateprocessid": "demo-process-000005",
          "base": "BTC",
          "quote": "USD",
          "collateralcurrency": "USD",
          "desireprice": 10,
          "lotssize": 1,
          "executeprice": 0,
          "ordertype": "Limit",
          "initprice": 50025.00
        }
      }
    }
  }
}
```

Position Event is an enum, so you have 5 possible options there;

| Name     | Type            | Description       |
|----------|-----------------|-------------------|
| Created  | PendingPosition | Created           |
| Updated  | PendingPosition | Updated           |
| Canceled | PendingPosition | Canceled          |
| Failed   | PendingPosition | Failed to execute |
| Executed | PendingPosition | Success execute   |