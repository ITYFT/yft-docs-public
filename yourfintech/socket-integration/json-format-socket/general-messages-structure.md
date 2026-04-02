# General messages structure

## Overview

Each message exchanged over the TCP socket is a JSON object adhering to the following general structure:

```json
{
  "messageid": "string",
  "messagetype": {
    // Message type content
  }
}
```

| Name         | Type                      | Desc                                                                                                                                                                |
|--------------|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Message id   | String                    | A unique identifier for the message, represented as a string. This ID can be used for tracking messages, correlating requests and responses, or debugging purposes. |
| Message type | YftIntegrationMessageType | object or string that specifies the type of the message and contains any associated data. The  message_type  field can be one of the following:                     |

## Message Types

### ClientMessage

Represents a message sent from the client to the server. The structure includes client-specific data encapsulated within the [`ClientMessage`](client-messages-spec.md) object.

```json
{
  "messageid": "string",
  "messagetype": {
    "clientmessage": //client message contract
  }
}
```

### ServerMessage

Represents a message sent from the server to the client. This message type contains server-specific data within the [`ServerMessage`](server-messages-spec.md) object.

```json
{
  "messageid": "string",
  "messagetype": {
    "servermessage": //client message contract
  }
}
```

### Ping

The server sends a heartbeat message to check the client's availability. The client **must** respond with a `Pong` message; otherwise, the server may disconnect the client.

```json
{
  "messageid": "test",
  "messagetype": "Ping"
}
```

### Pong

A response to a `Ping` message, indicating that the sender is active and the connection is alive.

```json
{
  "messageid": "test",
  "messagetype": "Pong"
}
```