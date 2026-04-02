# Client messages spec

The `YftJsonIntegrationClientMessage` enum defines the different types of messages that the client can send to the server. Each message type corresponds to specific information or events that the server will handle.

Message Types:

- AuthRequest

## AuthRequest

The `AuthRequest` message communicates the outcome of an authentication attempt by the client.

```json
{
  "messageid": "test",
  "messagetype": {
    "clientmessage": {
      "authrequest": {
        "secretkey": "secret"
      }
    }
  }
}

```