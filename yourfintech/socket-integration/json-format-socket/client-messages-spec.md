# Client messages spec

The `YftJsonIntegrationClientMessage` enum defines the different types of messages that the client can send to the server. Each message type corresponds to specific information or events that the server will handle.

Message types:

- `auth_request`

## auth_request

The `auth_request` message authenticates the client against the socket. The server replies
with [`auth_result`](server-messages-spec.md#auth_result).

```json
{
  "message_id": "test",
  "message_type": {
    "client_message": {
      "auth_request": {
        "secret_key": "secret"
      }
    }
  }
}
```

| Field | Type | Description |
| --- | --- | --- |
| secret_key | String | The integration secret key issued for this connection. |
