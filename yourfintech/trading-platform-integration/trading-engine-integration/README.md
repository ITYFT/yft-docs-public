# Trading Engine Integration

# Messages and Data Structures

## EngineActivePositionGrpcModel

Represents an active position with all its details.

```protobuf
message EngineActivePositionGrpcModel {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    string AssetPair = 4;
    EnginePositionSideGrpcModel Side = 5;
    double ContractSize = 6;
    double LotsAmount = 7;
    uint64 Created = 8;
    string ProcessIdCreate = 9;
    uint64 LastUpdateDate = 10;
    string LastUpdateProcessId = 11;
    string Base = 12;
    string Quote = 13;
    string Collateral = 14;

    optional double TpInProfit = 15;
    optional double SlInProfit = 16;
    optional double TpInAssetPrice = 17;
    optional double SlInAssetPrice = 18;
    map<string, string> Metadata = 19;

    string OpenProcessId = 20;
    uint64 OpenDate = 21;
    EngineBidAsk OpenBidAsk = 22;
    double OpenPrice = 23;
    EngineBidAsk ActiveBidAsk = 24;
    double ActivePrice = 25;
    EngineBidAsk MarginBidAsk = 26;
    double MarginPrice = 27;
    EngineBidAsk ProfitBidAsk = 28;
    double ProfitPrice = 29;
    double Profit = 30;
    double Commissions = 31;
    double Swaps = 32;
}
```

---

## EngineClosedPositionGrpcModel

Represents a closed position with all its details.

```protobuf
message EngineClosedPositionGrpcModel {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    string AssetPair = 4;
    EnginePositionSideGrpcModel Side = 5;
    double ContractSize = 6;
    double LotsAmount = 7;
    uint64 Created = 8;
    string ProcessIdCreate = 9;
    uint64 LastUpdateDate = 10;
    string LastUpdateProcessId = 11;
    string Base = 12;
    string Quote = 13;
    string Collateral = 14;

    optional double TpInProfit = 15;
    optional double SlInProfit = 16;
    optional double TpInAssetPrice = 17;
    optional double SlInAssetPrice = 18;
    map<string, string> Metadata = 19;

    string OpenProcessId = 20;
    uint64 OpenDate = 21;
    EngineBidAsk OpenBidAsk = 22;
    double OpenPrice = 23;
    EngineBidAsk ActiveBidAsk = 24;
    double ActivePrice = 25;
    EngineBidAsk MarginBidAsk = 26;
    double MarginPrice = 27;
    EngineBidAsk ProfitBidAsk = 28;
    double ProfitPrice = 29;
    double Profit = 30;

    string CloseProcessId = 31;
    uint64 CloseDate = 32;
    EngineBidAsk CloseBidAsk = 33;
    double ClosePrice = 34;
    EngineClosePositionReason CloseReason = 35;
}
```

---

## EnginePendingOrderGrpcModel

Represents a pending order.

```protobuf
message EnginePendingOrderGrpcModel {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    string AssetPair = 4;
    EnginePositionSideGrpcModel Side = 5;
    double ContractSize = 6;
    double LotsAmount = 7;
    uint64 Created = 8;
    string ProcessIdCreate = 9;
    uint64 LastUpdateDate = 10;
    string LastUpdateProcessId = 11;
    string Base = 12;
    string Quote = 13;
    string Collateral = 14;

    optional double TpInProfit = 15;
    optional double SlInProfit = 16;
    optional double TpInAssetPrice = 17;
    optional double SlInAssetPrice = 18;
    map<string, string> Metadata = 19;

    double DesirePrice = 20;
    EnginePendingOrderType OrderType = 21;
}
```

---

## EngineBidAsk

Represents the bid/ask prices for an instrument.

```protobuf
message EngineBidAsk {
    string Symbol = 1;
    double Bid = 2;
    double Ask = 3;
    uint64 Timestamp = 4;
}
```

---

## EngineOperationCode

Defines possible operation statuses.

```protobuf
enum EngineOperationCode {
    Ok = 0;
    DayOff = 1;
    OperationIsTooLow = 2;
    OperationIsTooHigh = 3;
    MinLotsSizeReached = 4;
    MaxLotsSizeReached = 5;
    NotEnoughBalance = 6;
    NoLiquidity = 7;
    PositionNotFound = 8;
    TpIsTooClose = 9;
    SlIsTooClose = 10;
    AccountNotFound = 11;
    InstrumentNotFound = 12;
    InstrumentIsNotTradable = 13;
    HitMaxAmountOfPendingOrders = 14;
    TechError = 15;
    MultiplierIsNotFound = 16;
    TradingDisabled = 17;
    MaxPositionsAmount = 18;
    TradingGroupNotFound = 19;
    TradingProfileNotFound = 20;
    TradingProfileInstrumentNotFound = 21;
    ABookReject = 22;
    ProcessIdDuplicate = 23;
}
```

---

## EngineClosePositionReason

Defines reasons for closing a position.

```protobuf
enum EngineClosePositionReason {
    TakeProfit = 0;
    StopLoss = 1;
    StopOut = 2;
    ClientCommand = 3;
    AdminCommand = 4;
}
```

---

## EnginePositionSideGrpcModel

Defines the position side (buy/sell).

```protobuf
enum EnginePositionSideGrpcModel {
    Buy = 0;
    Sell = 1;
}
```

---

## EnginePendingOrderType

Defines the type of pending order.

```protobuf
enum EnginePendingOrderType {
    Limit = 0;
    Stop = 1;
}
```

---

# Contracts

## OpenPendingOrderGrpcRequest

Used to place a pending order.

```protobuf
message OpenPendingOrderGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    string ProcessId = 3;
    string Instrument = 4;
    double Lots = 5;
    EnginePositionSideGrpcModel Side = 6;
    double DesiredPrice = 7;
    optional double TpInCurrency = 8;
    optional double SlInCurrency = 9;
    optional double TpInAssetPrice = 10;
    optional double SlInAssetPrice = 11;
    map<string, string> Metadata = 12;
    EnginePendingOrderType OrderType = 13;
}
```

---

## OpenPendingGrpcResponse

Response message for placing a pending order.

```protobuf
message OpenPendingGrpcResponse {
    EngineOperationCode Status = 1;
    optional EnginePendingOrderGrpcModel Order = 2;
}
```

---

## CancelPendingOrderGrpcRequest

Used to cancel a pending order.

```protobuf
message CancelPendingOrderGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
}
```

---

## CancelPendingGrpcResponse

Response message for canceling a pending order.

```protobuf
message CancelPendingGrpcResponse {
    EngineOperationCode Status = 1;
    optional EnginePendingOrderGrpcModel Order = 2;
}
```

---

## UpdatePendingOrderSlTpGrpcRequest

Used to update the Stop Loss and Take Profit levels for a pending order.

```protobuf
message UpdatePendingOrderSlTpGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    optional double TpInCurrency = 4;
    optional double SlInCurrency = 5;
    optional double TpInAssetPrice = 6;
    optional double SlInAssetPrice = 7;
    string ProcessId = 8;
}
```

---

## UpdatePendingOrderTpSlResponse

Response message for updating Stop Loss and Take Profit for a pending order.

```protobuf
message UpdatePendingOrderTpSlResponse {
    EngineOperationCode Status = 1;
    optional EnginePendingOrderGrpcModel Position = 2;
}
```

---

## GetPendingOrdersGrpcRequest

Used to retrieve all pending orders.

```protobuf
message GetPendingOrdersGrpcRequest {
    optional string TraderId = 1;
    optional string AccountId = 2;
}
```

---

## OpenPositionGrpcRequest

Used to open a new active position.

```protobuf
message OpenPositionGrpcRequest {
    string TraderId = 1;
    string AccountId =

 2;
    string ProcessId = 3;
    string Instrument = 4;
    double Lots = 5;
    EnginePositionSideGrpcModel Side = 6;
    optional double TpInCurrency = 7;
    optional double SlInCurrency = 8;
    optional double TpInAssetPrice = 9;
    optional double SlInAssetPrice = 10;
    map<string, string> Metadata = 11;
}
```

---

## OpenPositionGrpcResponse

Response message for opening a position.

```protobuf
message OpenPositionGrpcResponse {
    EngineOperationCode Status = 1;
    optional EngineActivePositionGrpcModel Position = 2;
}
```

---

## ClosePositionGrpcRequest

Used to close an active position.

```protobuf
message ClosePositionGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    string ProcessId = 4;
    EngineClosePositionReason Reason = 5;
}
```

---

## ClosePositionGrpcResponse

Response message for closing a position.

```protobuf
message ClosePositionGrpcResponse {
    EngineOperationCode Status = 1;
    optional EngineClosedPositionGrpcModel Position = 2;
}
```

---

## UpdateActiveSlTpGrpcRequest

Used to update the Stop Loss and Take Profit levels for an active position.

```protobuf
message UpdateActiveSlTpGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    optional double TpInCurrency = 4;
    optional double SlInCurrency = 5;
    optional double TpInAssetPrice = 6;
    optional double SlInAssetPrice = 7;
    string ProcessId = 8;
}
```

---

## UpdateActiveSlTpGrpcResponse

Response message for updating Stop Loss and Take Profit for an active position.

```protobuf
message UpdateActiveSlTpGrpcResponse {
    EngineOperationCode Status = 1;
    optional EngineClosedPositionGrpcModel Position = 2;
}
```

---

## GetActivePositionsGrpcRequest

Used to retrieve all active positions.

```protobuf
message GetActivePositionsGrpcRequest {
    optional string TraderId = 1;
    optional string AccountId = 2;
}
```

---

## gRPC Service: `EngineIntegrationGrpcService`

This service manages all trading operations, including pending orders, active positions, and position updates.

```protobuf
service EngineIntegrationGrpcService {
    rpc OpenPending(OpenPendingOrderGrpcRequest) returns (OpenPendingGrpcResponse);
    rpc CancelPending(CancelPendingOrderGrpcRequest) returns (CancelPendingGrpcResponse);
    rpc UpdatePendingSlTp(UpdatePendingOrderSlTpGrpcRequest) returns (UpdatePendingOrderTpSlResponse);
    rpc GetPendingPositions(GetPendingOrdersGrpcRequest) returns (stream EnginePendingOrderGrpcModel);

    rpc OpenPosition(OpenPositionGrpcRequest) returns (OpenPositionGrpcResponse);
    rpc ClosePosition(ClosePositionGrpcRequest) returns (ClosePositionGrpcResponse);
    rpc UpdateActiveSlTp(UpdateActiveSlTpGrpcRequest) returns (UpdateActiveSlTpGrpcResponse);
    rpc GetActivePositions(GetActivePositionsGrpcRequest) returns (stream EngineActivePositionGrpcModel);
}
```

* [ProtoFile](protofile.md)
