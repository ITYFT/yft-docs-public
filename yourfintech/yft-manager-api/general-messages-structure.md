# General messages structure

Each message exchanged over the TCP socket in **YFT Manager API** is a JSON object with the following general structure:

```json
{
  "message_id": "string",
  "message_response_id": "string | null",
  "message_type": {
    // See one of the message types
  }
}
```

## Structure

| Name                  | Type                       | Description                                                                                                       |
| --------------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `message_id`          | String                     | Unique identifier for the message. Used to trace or correlate requests and responses. Required for every message. |
| `message_response_id` | String (optional)          | Refers to the original `message_id` in case this is a response to a client's request.                             |
| `message_type`        | `YftManagerApiMessageType` | The actual type of the message. Defines the content and behavior.                                                 |

### Message Type

The `message_type` field is an enum and can be one of:

* `ClientMessage` — describes client-initiated messages (requests).\
  See details on the Client messages spec page.
* `ServerMessage` — describes server responses and updates.\
  See details on the Server messages spec page.
* `Ping` — heartbeat ping from the client.
* `Pong` — heartbeat pong from the server.

#### Expamples:

### Basic structure of `ClientMessage`

```json
{
  "message_id": "string",
  "message_response_id": "string | null",
  "message_type": {
    "client_message": {
      // One of client messages, e.g. { "auth_request": { "secret_key": "..." } }
    }
  }
}
```

Basic structure of **`ServerMessage`**

**Event Messages**

Used for broadcasting real-time updates or snapshots to all or subscribed clients.

```json
{
  "message_id": "string",               // Unique message ID (UUID)
  "message_response_id": null,         // Always null for event messages
  "message_type": {
    "server_message": {
      "positions": {
        "snapshot": [
          // Full array of domain objects (e.g. positions)
        ]
      }
    }
  }
}
```

Or for updates:

```json
{
  "message_id": "string",
  "message_response_id": null,
  "message_type": {
    "server_message": {
      "positions": {
        "update": {
          // Single object or array of updated domain objects
        }
      }
    }
  }
}
```

Or for errors

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "message_id": "string",
  "message_response_id": null,
  "message_type": {
    "server_message": {
      "positions": {
        "error": "unauthorized"
      }
    }
  }
} 
</code></pre>

**Response Messages**

Used for responses to client-initiated requests (e.g., `get_positions`, `auth_request`, etc.).

```json
{
  "message_id": "string",
  "message_response_id": "uuid", // Refers to original request message_id
  "message_type": {
    "server_message": {
      "get_positions_response": {
        "success": [
          // Array of positions
        ]
      }
    }
  }
}
```

**Error Message**

```json
{
  "message_id": "string",
  "message_response_id": "uuid|null", // Present only if it's a response error
  "message_type": {
    "server_message": {
      "get_positions_response": {
        "error": "unauthorized"
      }
    }
  }
}
```

See more detail on [ServerMessage](server-message-spec.md) for full list of supported `<payload_key>` and `<event_type>` values.

### Ping/Pong

The `Ping` message is used to check the availability and liveness of a participant in the TCP connection. It serves as a **heartbeat mechanism** to ensure that the socket is still active and responsive.

Dual Responsibility

All clients **must support both** sending and receiving ping-related messages:

* **Send `Ping`** messages to keep the connection alive.
* **Respond with `Pong`** to incoming `Ping` messages from the server (if implemented).

**Manager Connection Specifics**

When connected to the **Manager TCP socket**, the client **must initiate a heartbeat** by sending a `Ping` message **at least once every 3 seconds**. This is a strict requirement — if the client fails to send `Ping` messages within the expected interval, the server may treat the connection as dead and forcibly disconnect it.

This behavior allows the server to avoid tracking stale or broken connections and helps maintain consistent state synchronization across clients.

Ping structure

```json
{
  "message_id": "string",
  "message_response_id": "string | null",
  "message_type": "ping"
}
```

Pong structure

```json
{
  "message_id": "string",
  "message_response_id": "string | null",
  "message_type": "pong"
}
```

## message\_response\_id

### Behavior and Purpose

The `message_response_id` field is used to **correlate a server response with a specific client request**. It allows the client to identify which request the server is responding to, especially in asynchronous or concurrent environments.

***

#### When It Is Present

* `message_response_id` is **included** in all server messages that are **direct responses** to a client-initiated request.
* It **matches the `message_id`** of the original client request.

**Examples of server messages where `message_response_id` is present:**

* `auth_response`
* `get_accounts_response`
* `get_positions_response`
* `update_balance_response`
* `subscribe_result`
* etc.

***

#### When It Is Absent

* `message_response_id` is **absent** (i.e., `null`) in all **event-type messages**, which are sent by the server independently from any specific request.
* These messages are typically snapshots or updates broadcasted due to data changes or subscriptions.

**Examples of messages where `message_response_id` is always `null`:**

* `accounts` (snapshot/update)
* `positions` (snapshot/update)
* `trades` (update)
* `last_prices` (snapshot/update)
* etc.

### Error Responses

Errors from the server can appear in two general forms:

#### 1. **Error response to a client request**

When the client sends an invalid or unauthorized message, the server responds with an error message **tied to the original request**.

* `message_response_id` is **required**
* It references the original `message_id` of the client's request
* This allows the client to **correlate the error with the request it sent**

**Example**

```json
{
  "message_id": "b6a432e1-e268-4a90-a7e8-8e832dd2184b",
  "message_response_id": "e4bd1a9d-02a4-43f1-a1b7-8ae328cfbfe7",
  "message_type": {
    "server_message": {
      "get_accounts_response": {
        "error": "unauthorized"
      }
    }
  }
}
```

***

2\. **Event-type error (unrelated to a request)**

In some cases, the server may broadcast an error not tied to any specific client message — for example, failure in streaming updates or deserialization of an invalid message.

* `message_response_id` is always **`null`**
* These messages are not related to a specific client request
* Typically used in cases like:
  * Subscribed stream failed to deliver
  * A client sends malformed data that could not be parsed
  * Internal server issue affecting event dispatching

**Examples**

```json
{
  "message_id": "a63f2b78-fc68-4cf5-89fc-f0c42c2e0370",
  "message_response_id": null,
  "message_type": {
    "server_message": {
      "error": "invalid_message_format"
    }
  }
}
```

```json
{
  "message_id": "1741a4f1-c170-4a0d-9858-9a0c0f86f9dc",
  "message_response_id": null,
  "message_type": {
    "server_message": {
      "positions": {
        "error": "unexpected"
      }
    }
  }
}
```

***

#### Summary Table

| Scenario                        | `message_response_id` | Meaning                                     |
| ------------------------------- | --------------------- | ------------------------------------------- |
| Success response                | ✅ Present             | Points to the original client request       |
| Error response (to a request)   | ✅ Present             | Points to the original client request       |
| Event (snapshot/update)         | ❌ Always `null`       | Not a response to a client request          |
| Event error (e.g., stream fail) | ❌ Always `null`       | Not linked to a specific client interaction |

***

#### Field Type

```json
"message_response_id": "string" | null
```

* A valid **UUID string** when the message is a response
* Always `null` in event/broadcast messages

***

### Client Guidance

* Use `message_response_id` to track and resolve pending requests
* Ignore `message_response_id` for snapshot, update, and subscription events
* When `server_message.error` appears outside a response context, treat it as a **system-level broadcast**

### ID Types

In our system, every primary entity (such as an account, position, or order) is identified by two types of IDs:

1. The UUID is the platform's primary, native identifier. It's a long, unique string (e.g., `123e4567-e89b-12d3-a456-426614174000`) that guarantees global uniqueness, making the system highly reliable.
2. The Numeric ID (or `num_id`) is a short, unique number (e.g., `1834567`) generated based on the UUID.

We support both ID types to provide flexibility for our API clients. Some systems find it more convenient to work with shorter numeric IDs, which are easier for humans to read and for some legacy systems to process.

***

### System Workflow

The process is designed to be seamless and consists of three stages:

1. Receiving a Request: Clients can send a request using either ID type. Our system understands both. If a numeric ID is provided, the server instantly finds its corresponding UUID in our database before proceeding.
2. Internal Processing: Internally, our system works exclusively with UUIDs to ensure maximum reliability and prevent ambiguity.
3. Sending a Response: When a response is ready, we check the client's preference, which was specified during the connection setup:
   * If the client requested UUIDs only, we send the data in its standard format.
   * If the client requested both UUIDs and Numeric IDs, our server finds the numeric pair for each UUID and adds it to the response before sending.
