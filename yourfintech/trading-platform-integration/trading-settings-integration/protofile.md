# ProtoFile

```protobuf
syntax = "proto3";
package cfdintegrationsettings;
import "google/protobuf/empty.proto";

//Models
message TradingInstrumentDayOffGrpcModel {
    int32 DowFrom = 1;
    string TimeFrom = 2;
    int32 DowTo = 3;
    string TimeTo = 4;
}

message TradingInstrumentGrpcModel {
    string Name = 1;
    int32 Digits = 2;
    string Base = 3;
    string Quote = 4;
    double TickSize = 5;
    optional string SwapScheduleId = 6; 
    optional string GroupId = 7;        
    optional int32 Weight = 8;          
    optional int32 DayTimeout = 9;      
    optional int32 NightTimeout = 10;   
    bool TradingDisabled = 11;
    double LotSize = 12;
    repeated TradingInstrumentDayOffGrpcModel DaysOff = 13;
    string Id = 14;
}

message TradingGroupGrpcModel {
    string Id = 1;
    string Name = 2;
    string TradingProfileId = 3;
    optional string MarkupProfileId = 4;
    string SwapProfileId = 5;
    bool TradingDisabled = 6;
}

message TradingProfileGrpcModel {
    string Id = 1;
    double StopOutPercent = 2;
    bool IsABook = 3;
    optional double MarginCallPercent = 4;
    repeated TradingProfileInstrumentGrpcModel Instruments = 5;
    repeated double Leverages = 6;
    repeated string CollateralCurrencies = 7;
    double InitialDeposit = 8;
    double HedgeMarginCoefficient = 9;
    optional double CommissionPerLot = 10;
}

message TradingProfileInstrumentGrpcModel {
    string Id = 1;
    double MinOperationVolume = 2;
    double MaxOperationVolume = 3;
    double MaxPositionVolume = 4;
    int32 OpenPositionMinDelayMs = 5;
    int32 OpenPositionMaxDelayMs = 6;
    optional double StopOutPercent = 7;
    optional double InstrumentMaxLeverage = 8;
    optional double CommissionPerLot = 9;
}

message SwapProfileGrpcModel {
    string Id = 1;
    string Name = 2;
    map<string, SwapScheduleTypeGrpcModel> InstrumentsSwaps = 3;
}

message SwapScheduleTypeGrpcModel {
    oneof SwapType {
        SwapProfileInstrumentGrpcModel Percent = 1;
        SwapProfileInstrumentGrpcModel Points = 2;
    }
}

message SwapProfileInstrumentGrpcModel {
    double Long = 1;
    double Short = 2;
    repeated string Schedules = 3;
}

message SwapScheduleGrpcModel {
    string Id = 1;
    string Name = 2;
    // Mon = 0,
    // Tue = 1,
    // Wed = 2,
    // Thu = 3,
    // Fri = 4,
    // Sat = 5,
    // Sun = 6,
    map<int32, SwapDayScheduleGrpcModel> WeekSchedules = 3;
}

message SwapDayScheduleGrpcModel {
    string Time = 1;
    double SwapMultiplier = 2;
}

message MarkupProfileGrpcModel {
    string Name = 1;
    bool Disabled = 2;
    map<string, MarkupInstrumentGrpcModel> Instruments = 3;
}

message MarkupInstrumentGrpcModel {
    int32 MarkupBid = 1;
    int32 MarkupAsk = 2;
    optional int32 MaxSpread = 3;
    optional int32 MinSpread = 4;
}

service TradingSettingsIntegrationGrpcService{
    rpc InsertOrReplaceInstrument(TradingInstrumentGrpcModel) returns (TradingInstrumentGrpcModel);
    rpc InsertOrReplaceTradingProfile(TradingProfileGrpcModel) returns (TradingProfileGrpcModel);
    rpc InsertOrReplaceTradingGroup(TradingGroupGrpcModel) returns (TradingGroupGrpcModel);
    rpc InsertOrReplaceTradingSwapProfile(SwapProfileGrpcModel) returns (SwapProfileGrpcModel);
    rpc InsertOrReplaceTradingSwapProfileSchedule(SwapScheduleGrpcModel) returns (SwapScheduleGrpcModel);
    rpc InsertOrReplaceTradingMarkupProfile(MarkupProfileGrpcModel) returns (MarkupProfileGrpcModel);

    rpc GetInstruments(google.protobuf.Empty) returns (stream TradingInstrumentGrpcModel);
    rpc GetTradingProfiles(google.protobuf.Empty) returns (stream TradingProfileGrpcModel);
    rpc GetTradingGroups(google.protobuf.Empty) returns (stream TradingGroupGrpcModel);
    rpc GetTradingSwapProfiles(google.protobuf.Empty) returns (stream SwapProfileGrpcModel);
    rpc GetTradingSwapProfileSchedules(google.protobuf.Empty) returns (stream SwapScheduleGrpcModel);
    rpc GetTradingMarkupProfiles(google.protobuf.Empty) returns (stream MarkupProfileGrpcModel);
}

```