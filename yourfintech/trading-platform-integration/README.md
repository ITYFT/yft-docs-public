# Trading Platform Integration

## Introduction

This document provides comprehensive guidance on integrating with the Yourfintech platform via gRPC and Protocol Buffers (proto3). The platform allows for managing accounts, executing cross-margin trades, placing pending orders, and configuring trading settings.

This documentation is intended for developers, system integrators, and engineers looking to integrate their systems with Yourfintech Trading Platform and perform key operations such as account creation, balance updates, and trade executions.

---

## System Requirements

**Supported Platforms:**

- **gRPC**: The Yourfintech platform leverages gRPC for all API interactions.
- **Protobuf Version**: Protocol Buffers version 3 (proto3).
- **SSL/TLS**: Secure communications are required for all production environments.

**Required Software:**

- **Protobuf Compiler**: Use the Protocol Buffers compiler (`protoc`) to generate client stubs.
- **gRPC Libraries**: Ensure you have the appropriate gRPC library for your language (e.g., grpc-java, grpc-python, grpc-go).

---

## Getting Started

## Step 1: Generating Client Stubs

The `.proto` files (`AccountsIntegration.proto`, `EngineIntegration.proto`, `TradingSettingsIntegration.proto`) are used to generate two types of client interfaces:

1. **Function Interfaces**: These handle interactions with gRPC (e.g., `AccountsIntegration_grpc_pb.*`).
2. **Data Interfaces**: These define the structure of requests and responses (e.g., `AccountsIntegration_pb.*`).

**Command (example in Python):**

```python
bashCopy codeprotoc --pythonout=. --grpcpythonout=. AccountsIntegration.proto
protoc --pythonout=. --grpcpythonout=. EngineIntegration.proto
protoc --pythonout=. --grpcpythonout=. TradingSettingsIntegration.proto
```

This command will generate both function interfaces and data interfaces necessary to work with the platform’s services.

## Step 2: SSL/TLS Configuration

Secure communication is mandatory when connecting to the Yourfintech Trading Platform in production environments. Set up SSL/TLS certificates and provide the certificate paths when initializing your gRPC client.

## Step 3: Authentication Setup

Authentication is required for API access. Contact support to receive API keys or OAuth tokens. These credentials should be included in each gRPC request.

## Error Codes and Troubleshooting

All operations return an operation code indicating the status of the request. These codes are defined in the respective `.proto` files.

### Common Error Codes for Trading Operations:

- **Ok (0)**: Operation was successful.
- **NotEnoughBalance (6)**: Insufficient balance for the operation.
- **InstrumentNotFound (12)**: Specified instrument not found or not tradable.
- **TradingDisabled (17)**: Trading is disabled for the account or instrument.

### Troubleshooting Tips:

- **Invalid Instrument**: Ensure the instrument symbol is correct.
- **Insufficient Balance**: Verify the account has enough funds before attempting a trade.
- **Duplicate Process IDs**: Always use a unique `ProcessId` for each operation to avoid duplication.

---

## Connection Setup

Before interacting with the **YFT CFD Cross-Margin Trading Platform**, the following connection setup is required:

1. **SSL/TLS Setup**: Configure SSL/TLS for secure communication in production environments.
2. **Authentication**: Include API tokens or OAuth credentials in each request.
3. **Connection via gRPC**:
      - After the connection is established, invoke the required function using the function interface (`*_grpc_pb.*`).
      - Send a valid request constructed from the data interface (`*_pb.*`).
      - Await the response and check for success or failure.

* [Account Integration](account-integration/README.md)
* [Trading Engine Integration](trading-engine-integration/README.md)
* [Trading Settings Integration](trading-settings-integration/README.md)
