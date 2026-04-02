# ProtoFile

```protobuf
syntax = "proto3";
package cfdintegrationaccounts;

//Models
message AccountGrpcModel{
    string Id = 1;
    string TraderId = 2;
    string Currency = 3;
    double Balance = 4;
    double Equity = 5;
    double Margin = 6;
    bool TradingDisabled = 7;
    double Leverage = 8;
    double FreeMargin = 9;
    double MarginLvl = 10;
    string TradingGroup = 11;
    uint64 DateCreate = 12;
    uint64 DateUpdate = 13;
    string CreateProcessId = 14;
    string LastUpdateProcessId = 15;
    map<string, string> Metadata = 16;
}

//Contracts
enum UpdateBalanceReason {
    TradingResult = 0;
    BalanceCorrection = 1;
    Deposit = 2;
    Withdrawal = 3;
    WithdrawalCanceled = 4;
    Commission = 5;
}

enum AccountsIntegrationOperationCode{
    Ok = 0;
    AccountNotFound = 1;
    TraderNotFound = 2;
    NotEnoughBalance = 3;
    ProcessIdDuplicate = 4;
    CollateralCurrencyNotFound = 5;
    InvalidLeverage = 6;
    TradingGroupNotFound = 7;
    TradingProfileNotFound = 8;
}

message CreateAccountGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    double Leverage = 3;
    string Currency = 4;
    string ProcessId = 5;
    string TradingGroup = 6;
    map<string, string> Metadata = 7;
}

message CreateAccountGrpcResponse{
    AccountsIntegrationOperationCode Code = 1;
    optional AccountGrpcModel Account = 2;
}

message GetAccountsGrpcRequest{
    optional string TraderId = 1;
    optional string AccountId = 2;
}

message UpdateAccountBalanceGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    double Delta = 3;
    string Comment = 4;
    string ProcessId = 5;
    bool AllowNegativeBalance = 6;
    UpdateBalanceReason Reason = 7;
    optional string ReferenceTransactionId = 8;
}

message UpdateAccountBalanceGrpcResponse{
    AccountsIntegrationOperationCode Result = 1;
    optional string OperationId = 2;
    AccountGrpcModel Account = 3;
}

message UpdateAccountGrpcRequest{
    string TraderId = 1;
    string AccountId = 2;
    optional bool TradingDisabled = 3;
    optional string TradingGroup = 4;
    optional double Leverage = 5;
    string ProcessId = 6;
}

message UpdateAccountGrpcResponse{
    AccountsIntegrationOperationCode Result = 1;
    AccountGrpcModel Account = 2;
}

service AccountsIntegrationGrpcService{
    rpc CreateAccount(CreateAccountGrpcRequest) returns (CreateAccountGrpcResponse);
    rpc GetAccounts(GetAccountsGrpcRequest) returns (stream AccountGrpcModel);
    rpc UpdateAccountBalance(UpdateAccountBalanceGrpcRequest) returns (UpdateAccountBalanceGrpcResponse);
    rpc UpdateAccount(UpdateAccountGrpcRequest) returns (UpdateAccountGrpcResponse);
}

```