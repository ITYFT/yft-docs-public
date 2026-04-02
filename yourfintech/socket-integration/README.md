# Socket Integration

## Service Overview

Our service offers a robust API for real-time financial data streaming and comprehensive trading operations.

We provide two specialized endpoints to segregate pricing data from trading operations for optimized performance and security.

## Data Transmission Format

**All messages exchanged between the client and server adhere to the following format:**

- JSON Serialization: Messages are serialized using JSON for easy integration
- Messages Separation: Each message is terminated with a [13,10] (\r\n) bytes to delineate individual messages in the communication stream.

## Authentification

All clients must authenticate before accessing the Prices and Trading endpoints. Authentication is handled via exchanging protobuf or JSON messages and should be the first message sent after establishing a connection.

* [JSON format socket](json-format-socket/README.md)
