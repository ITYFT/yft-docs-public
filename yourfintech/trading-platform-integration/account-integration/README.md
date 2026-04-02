# Account Integration

# Messages and Data Structures

## AccountGrpcModel

Represents an account with its associated details.

```protobuf
message AccountGrpcModel {
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
```

**Fields:**

| Order | Name                | Description                                           |
|-------|---------------------|-------------------------------------------------------|
| 1     | Id                  | Unique identifier of the account.                     |
| 2     | TraderId            | Identifier of the trader associated with the account. |
| 3     | Currency            | Currency of the account (e.g., USD, EUR).             |
| 4     | Balance             | Current balance of the account.                       |
| 5     | Equity              | Equity of the account.                                |
| 6     | Margin              | Margin being used by the account.                     |
| 7     | TradingDisabled     | Indicates if trading is disabled for this account.    |
| 8     | Leverage            | Leverage applied to the account.                      |
| 9     | FreeMargin          | Free margin available in the account.                 |
| 10    | MarginLvl           | Margin level of the account.                          |
| 11    | TradingGroup        | The trading group this account belongs to.            |
| 12    | DateCreate          | Timestamp of account creation (Unix ms).              |
| 13    | DateUpdate          | Last update timestamp of the account (Unix ms).       |
| 14    | CreateProcessId     | Process ID used to create the account.                |
| 15    | LastUpdateProcessId | Process ID of the last update made to the account.    |
| 16    | Metadata            | Additional key-value pairs with extra information.    |

---

## UpdateBalanceReason

Defines the types of balance operations that can occur on an account.

```protobuf
enum UpdateBalanceReason {
    TradingResult = 0;
    BalanceCorrection = 1;
    Deposit = 2;
    Withdrawal = 3;
    WithdrawalCanceled = 4;
    Commission = 5;
}
```

**Fields:**

| Order | Name               | Description                                |
|-------|--------------------|--------------------------------------------|
| 0     | TradingResult      | Balance changes due to trading activities. |
| 1     | BalanceCorrection  | Manual corrections to the account balance. |
| 2     | Deposit            | Funds deposited into the account.          |
| 3     | Withdrawal         | Funds withdrawn from the account.          |
| 4     | WithdrawalCanceled | Canceled withdrawal operation.             |
| 5     | Commission         | Deductions due to commissions.             |

---

## AccountsIntegrationOperationCode

Defines operation results returned by account operations.

```protobuf
enum AccountsIntegrationOperationCode {
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
```

**Fields:**

| Order | Name                       | Description                                                  |
|-------|----------------------------|--------------------------------------------------------------|
| 0     | Ok                         | Operation was successful.                                    |
| 1     | AccountNotFound            | The account was not found.                                   |
| 2     | TraderNotFound             | The trader associated with the operation was not found.      |
| 3     | NotEnoughBalance           | Insufficient balance for the operation.                      |
| 4     | ProcessIdDuplicate         | The provided process ID already exists.                      |
| 5     | CollateralCurrencyNotFound | Collateral currency not found.                               |
| 6     | InvalidLeverage            | The specified leverage is invalid.                           |
| 7     | TradingGroupNotFound       | The trading group associated with the account was not found. |
| 8     | TradingProfileNotFound     | The trading profile was not found.                           |

---

# Contracts

## CreateAccountGrpcRequest

Used to create a new trader account.

```protobuf
message CreateAccountGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    double Leverage = 3;
    string Currency = 4;
    string ProcessId = 5;
    string TradingGroup = 6;
    map<string, string> Metadata = 7;
}
```

| Order | Name         | Description                                     |
|-------|--------------|-------------------------------------------------|
| 1     | TraderId     | Identifier of the trader who owns the account.  |
| 2     | AccountId    | Unique identifier for the account.              |
| 3     | Leverage     | Leverage ratio applied to the account.          |
| 4     | Currency     | Currency code for the account (e.g., USD, EUR). |
| 5     | ProcessId    | Unique process identifier for the operation.    |
| 6     | TradingGroup | Group the account is associated with.           |
| 7     | Metadata     | Key-value pairs for additional data.            |

---

## CreateAccountGrpcResponse

Response message for account creation.

```protobuf
message CreateAccountGrpcResponse {
    AccountsIntegrationOperationCode Code = 1;
    optional AccountGrpcModel Account = 2;
}
```

| Order | Name    | Description                                |
|-------|---------|--------------------------------------------|
| 1     | Code    | Operation result ( Ok  if successful).     |
| 2     | Account | Account details if creation is successful. |

---

## GetAccountsGrpcRequest

Used to retrieve accounts based on a trader ID or account ID.

```protobuf
message GetAccountsGrpcRequest {
    optional string TraderId = 1;
    optional string AccountId = 2;
}
```

| Order | Name      | Description                    |
|-------|-----------|--------------------------------|
| 1     | TraderId  | Optional filter by trader ID.  |
| 2     | AccountId | Optional filter by account ID. |

---

## UpdateAccountBalanceGrpcRequest

Used to update the balance of a trader's account.

```protobuf
message UpdateAccountBalanceGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    double Delta = 3;
    string Comment = 4;
    string ProcessId = 5;
    bool AllowNegativeBalance = 6;
    x Reason = 7;
    optional string ReferenceTransactionId = 8;
}
```

| Order | Name                   | Description                                                |
|-------|------------------------|------------------------------------------------------------|
| 1     | TraderId               | Identifier of the trader associated with the account.      |
| 2     | AccountId              | Identifier of the account being updated.                   |
| 3     | Delta                  | Amount to add or subtract from the account balance.        |
| 4     | Comment                | Optional comment or note for the operation.                |
| 5     | ProcessId              | Unique process identifier for the balance update.          |
| 6     | AllowNegativeBalance   | Whether negative balances are allowed for the operation.   |
| 7     | Reason                 | Reason for the balance update (see  UpdateBalanceReason ). |
| 8     | ReferenceTransactionId | Reference to a related transaction, if applicable.         |

---

## UpdateAccountBalanceGrpcResponse

Response message for updating an account balance.

```protobuf
message UpdateAccountBalanceGrpcResponse {
    AccountsIntegrationOperationCode Result = 1;
    optional string OperationId = 2;
    AccountGrpcModel Account = 3;
}
```

| Order | Name        | Description                                    |
|-------|-------------|------------------------------------------------|
| 1     | Result      | Operation result ( Ok  if successful).         |
| 2     | OperationId | Optional identifier for the balance operation. |
| 3     | Account     | Account details after the balance update.      |

---

## UpdateAccountGrpcRequest

Used to update the settings of an existing account.

```protobuf
message UpdateAccountGrpcRequest {
    string TraderId = 1;
    string AccountId = 2;
    optional bool TradingDisabled = 3;
    optional string TradingGroup = 4;
    optional double Leverage = 5;
    string ProcessId = 

6;
}
```

| Order | Name            | Description                                           |
|-------|-----------------|-------------------------------------------------------|
| 1     | TraderId        | Identifier of the trader.                             |
| 2     | AccountId       | Identifier of the account to update.                  |
| 3     | TradingDisabled | Optional: Disable or enable trading on the account.   |
| 4     | TradingGroup    | Optional: Update the trading group of the account.    |
| 5     | Leverage        | Optional: Update the leverage applied to the account. |
| 6     | ProcessId       | Unique process ID for the operation.                  |

---

## UpdateAccountGrpcResponse

Response message for updating account settings.

```protobuf
message UpdateAccountGrpcResponse {
    AccountsIntegrationOperationCode Result = 1;
    AccountGrpcModel Account = 2;
}
```

| Order | Name    | Description                            |
|-------|---------|----------------------------------------|
| 1     | Result  | Operation result ( Ok  if successful). |
| 2     | Account | Updated account details.               |

---

### gRPC Service: AccountsIntegrationGrpcService

This service provides account management functionality.

```protobuf
service AccountsIntegrationGrpcService {
    rpc CreateAccount(CreateAccountGrpcRequest) returns (CreateAccountGrpcResponse);
    rpc GetAccounts(GetAccountsGrpcRequest) returns (stream AccountGrpcModel);
    rpc UpdateAccountBalance(UpdateAccountBalanceGrpcRequest) returns (UpdateAccountBalanceGrpcResponse);
    rpc UpdateAccount(UpdateAccountGrpcRequest) returns (UpdateAccountGrpcResponse);
}
```

- `CreateAccount`: Creates a new account for a trader.
- `GetAccounts`: Retrieves accounts based on optional trader or account ID filters.
- `UpdateAccountBalance`: Updates the balance of an account.
- `UpdateAccount`: Modifies account settings.
- `Ping`: Health check for the service.

---

---

* [ProtoFile](protofile.md)
