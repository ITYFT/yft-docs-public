# Trading Settings Integration

# Messages and Data Structures

## TradingInstrumentGrpcModel

Represents a trading instrument with its associated parameters.

```protobuf
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
}
```

**Fields:**

| Order | Name            | Description                                                |
|-------|-----------------|------------------------------------------------------------|
| 1     | `Name`            | The name of the trading instrument (e.g., EUR/USD).        |
| 2     | `Digits`          | Number of decimal places for instrument prices.            |
| 3     | `Base`            | The base currency of the instrument.                       |
| 4     | `Quote`           | The quote currency of the instrument.                      |
| 5     | `TickSize`        | Minimum price increment for the instrument.                |
| 6     | `SwapScheduleId`  | ID of the swap schedule (optional).                        |
| 7     | `GroupId`         | ID of the instrument group (optional).                     |
| 8     | `Weight`          | Weight of the instrument for processing (optional).        |
| 9     | `DayTimeout`      | Day trading session timeout (optional).                    |
| 10    | `NightTimeout`    | Night trading session timeout (optional).                  |
| 11    | `TradingDisabled` | Indicates whether trading is disabled for this instrument. |
| 12    | `LotSize`         | The standard lot size for this instrument.                 |
| 13    | `DaysOff`         | List of trading session days off.                          |

---

## TradingInstrumentDayOffGrpcModel

Represents a day off for a trading instrument.

```protobuf
message TradingInstrumentDayOffGrpcModel {
    int32 DowFrom = 1;
    string TimeFrom = 2;
    int32 DowTo = 3;
    string TimeTo = 4;
}
```

**Fields:**

| Order | Name     | Description                                       |
|-------|----------|---------------------------------------------------|
| 1     | `DowFrom`  | Start day of the week (0 = Sunday, 6 = Saturday). |
| 2     | `TimeFrom` | Start time for the day off (HH:mm format).        |
| 3     | `DowTo`    | End day of the week.                              |
| 4     | `TimeTo`   | End time for the day off (HH:mm format).          |

---

## TradingGroupGrpcModel

Represents a trading group with its settings.

```protobuf
message TradingGroupGrpcModel {
    string Id = 1;
    string Name = 2;
    string TradingProfileId = 3;
    optional string MarkupProfileId = 4;
    string SwapProfileId = 5;
    bool TradingDisabled = 6;
}
```

**Fields:**

| Order | Name             | Description                                      |
|-------|------------------|--------------------------------------------------|
| 1     | `Id`               | Unique identifier for the trading group.         |
| 2     | `Name`             | Name of the trading group.                       |
| 3     | `TradingProfileId` | ID of the associated trading profile.            |
| 4     | `MarkupProfileId`  | ID of the associated markup profile (optional).  |
| 5     | `SwapProfileId`    | ID of the associated swap profile.               |
| 6     | `TradingDisabled`  | Indicates if trading is disabled for this group. |

---

## TradingProfileGrpcModel

Represents a trading profile with its conditions.

```protobuf
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
```

**Fields:**

| Order | Name                   | Description                                         |
|-------|------------------------|-----------------------------------------------------|
| 1     | `Id`                     | Unique identifier for the trading profile.          |
| 2     | `StopOutPercent`         | Stop-out percentage for this profile.               |
| 3     | `IsABook`                | Indicates if this profile uses A-Book execution.    |
| 4     | `MarginCallPercent`      | Margin call percentage (optional).                  |
| 5     | `Instruments`            | List of associated instruments with their settings. |
| 6     | `Leverages`              | List of available leverage levels for this profile. |
| 7     | `CollateralCurrencies`   | Currencies used as collateral.                      |
| 8     | `InitialDeposit`         | Minimum initial deposit required.                   |
| 9     | `HedgeMarginCoefficient` | Hedge margin coefficient for this profile.          |
| 10    | `CommissionPerLot`       | Commission per lot traded (optional).               |

---

## TradingProfileInstrumentGrpcModel

Represents instrument-specific settings within a trading profile.

```protobuf
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
```

---

## SwapProfileGrpcModel

Represents a swap profile.

```protobuf
message SwapProfileGrpcModel {
    string Id = 1;
    string Name = 2;
    map<string, SwapScheduleTypeGrpcModel> InstrumentsSwaps = 3;
}
```

---

## SwapScheduleTypeGrpcModel

Represents the swap schedule type.

```protobuf
message SwapScheduleTypeGrpcModel {
    oneof SwapType {
        SwapProfileInstrumentGrpcModel Percent = 1;
        SwapProfileInstrumentGrpcModel Points = 2;
    }
}
```

---

## SwapProfileInstrumentGrpcModel

Represents swap values for an instrument.

```protobuf
message SwapProfileInstrumentGrpcModel {
    double Long = 1;
    double Short = 2;
    repeated string Schedules = 3;
}
```

---

## SwapScheduleGrpcModel

Represents a swap schedule.

```protobuf
message SwapScheduleGrpcModel {
    string Id = 1;
    string Name = 2;
    map<int32, SwapDayScheduleGrpcModel> WeekSchedules = 3;
}
```

---

## SwapDayScheduleGrpcModel

Represents a daily swap schedule.

```protobuf
message SwapDayScheduleGrpcModel {
    string Time = 1;
    double SwapMultiplier = 2;
}
```

---

## MarkupProfileGrpcModel

Represents a markup profile.

```protobuf
message MarkupProfileGrpcModel {
    string Name = 1;
    bool Disabled = 2;
    map<string, MarkupInstrumentGrpcModel> Instruments = 3;
}
```

---

## MarkupInstrumentGrpcModel

Represents the markup settings for an instrument.

```protobuf
message MarkupInstrumentGrpcModel {
    int32 MarkupBid = 1;
    int32 MarkupAsk = 2;
    optional int32 MaxSpread = 3;
    optional int32 MinSpread = 4;
}
```

---

## GetInstrumentsFilterRequest

Represents the filter request for instruments.

```protobuf

message GetInstrumentsFilterRequest {
    optional string GroupId = 1;
    optional string Symbol = 2;
}
```

---

## gRPC Service: TradingSettingsIntegrationGrpcService

This service provides management for trading instruments, trading profiles, groups, swap schedules, and markup profiles.

```protobuf
service TradingSettingsIntegrationGrpcService{
    rpc InsertOrReplaceInstrument(TradingInstrumentGrpcModel) returns (TradingInstrumentGrpcModel);
    rpc InsertOrReplaceTradingProfile(TradingProfileGrpcModel) returns (TradingProfileGrpcModel);
    rpc InsertOrReplaceTradingGroup(TradingGroupGrpcModel) returns (TradingGroupGrpcModel);
    rpc InsertOrReplaceTradingSwapProfile(SwapProfileGrpcModel) returns (SwapProfileGrpcModel);
    rpc InsertOrReplaceTradingSwapProfileSchedule(SwapScheduleGrpcModel) returns (SwapScheduleGrpcModel);
    rpc InsertOrReplaceTradingMarkupProfile(MarkupProfileGrpcModel) returns (MarkupProfileGrpcModel);

    rpc GetInstruments(GetInstrumentsFilterRequest) returns (stream TradingInstrumentGrpcModel);
    rpc GetTradingProfiles(google.protobuf.Empty) returns (stream TradingProfileGrpcModel);
    rpc GetTradingGroups(google.protobuf.Empty) returns (stream TradingGroupGrpcModel);
    rpc GetTradingSwapProfiles(google.protobuf.Empty) returns (stream SwapProfileGrpcModel);
    rpc GetTradingSwapProfileSchedules(google.protobuf.Empty) returns (stream SwapScheduleGrpcModel);
    rpc GetTradingMarkupProfiles(google.protobuf.Empty) returns (stream MarkupProfileGrpcModel);
}
```

* [ProtoFile](protofile.md)
