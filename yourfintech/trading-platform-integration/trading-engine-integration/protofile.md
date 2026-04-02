# ProtoFile

```protobuf
syntax = "proto3";
package cfdintegrationengine;

//Models

enum EngineOperationCode{
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
    TradingProfileNotFound =20;
    TradingProfileInstrumentNotFound = 21;
    ABookReject = 22;
    ProcessIdDuplicate = 23;
}

enum EngineClosePositionReason{
    TakeProfit = 0;
    StopLoss = 1;
    StopOut = 2;
    ClientCommand = 3;
    AdminCommand = 4;
}

enum EnginePositionSideGrpcModel{
    Buy = 0;
    Sell = 1;
}

message EngineBidAsk{
    string Symbol = 1;
    double Bid = 2;
    double Ask = 3;
    uint64 Timestamp = 4;
}

enum EnginePendingOrderType{
    Limit = 0;
    Stop = 1;
}

message EngineActivePositionGrpcModel{
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

message EngineClosedPositionGrpcModel{
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

message EnginePendingOrderGrpcModel{
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

//Contracts

message OpenPendingOrderGrpcRequest{
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

message OpenPendingGrpcResponse{
    EngineOperationCode Status = 1;
    optional EnginePendingOrderGrpcModel Order = 2;
}

message CancelPendingOrderGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
}

message CancelPendingGrpcResponse{
    EngineOperationCode Status = 1;
    optional EnginePendingOrderGrpcModel Order = 2;
}

message UpdatePendingOrderSlTpGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    optional double TpInCurrency = 4;
    optional double SlInCurrency = 5;
    optional double TpInAssetPrice = 6;
    optional double SlInAssetPrice = 7;
    string ProcessId = 8;
}

message UpdatePendingOrderTpSlResponse{
    EngineOperationCode Status = 1;
    optional EnginePendingOrderGrpcModel Position = 2;
}

message GetPendingOrdersGrpcRequest{
    optional string TraderId = 1;
    optional string AccountId = 2;
}

message OpenPositionGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
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

message OpenPositionGrpcResponse{
    EngineOperationCode Status = 1;
    optional EngineActivePositionGrpcModel Position = 2;
}

message ClosePositionGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    string ProcessId = 4;
    EngineClosePositionReason Reason = 5;
}

message ClosePositionGrpcResponse{
    EngineOperationCode Status = 1;
    optional EngineClosedPositionGrpcModel Position = 2;
}

message UpdateActiveSlTpGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    string Id = 3;
    optional double TpInCurrency = 4;
    optional double SlInCurrency = 5;
    optional double TpInAssetPrice = 6;
    optional double SlInAssetPrice = 7;
    string ProcessId = 8;
}

message UpdateActiveSlTpGrpcResponse{
    EngineOperationCode Status = 1;
    optional EngineClosedPositionGrpcModel Position = 2;
}

message GetActivePositionsGrpcRequest{
    optional string TraderId = 1;
    optional string AccountId = 2;
}

service EngineIntegrationGrpcService{
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