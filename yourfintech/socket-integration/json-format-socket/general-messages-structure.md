# General messages structure

## Overview

Each message exchanged over the TCP socket is a JSON object adhering to the following general structure:

```json
{
  "message_id": "string",
  "message_type": {
    // Message type content
  }
}
```

All keys and all enum values on this socket are **snake_case**.

| Name | Type | Desc |
| --- | --- | --- |
| message_id | String | A unique identifier for the message. Use it to track messages, correlate requests and responses, or debug. |
| message_type | YftIntegrationMessageType | Object or string specifying the type of the message and carrying its data. It can be one of the following. |

## Message Types

### client_message

Represents a message sent from the client to the server. The structure includes client-specific data encapsulated within the [`client_message`](client-messages-spec.md) object.

```json
{
  "message_id": "string",
  "message_type": {
    "client_message": {} // client message contract
  }
}
```

### server_message

Represents a message sent from the server to the client. This message type contains server-specific data within the [`server_message`](server-messages-spec.md) object.

```json
{
  "message_id": "string",
  "message_type": {
    "server_message": {} // server message contract
  }
}
```

### ping

The server sends a heartbeat message to check the client's availability. The client **must** respond with a `pong` message; otherwise, the server may disconnect the client.

```json
{
  "message_id": "test",
  "message_type": "ping"
}
```

### pong

A response to a `ping` message, indicating that the sender is active and the connection is alive.

```json
{
  "message_id": "test",
  "message_type": "pong"
}
```
