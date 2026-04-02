# Yft Manager API

**YFT Manager API** is a high-performance TCP server designed for real-time monitoring and management of trading entities within a financial system.

The server implements a lightweight **JSON-over-TCP** protocol with fully typed message structures, providing:

- **Authentication** with strict request filtering
- **Initial snapshot delivery** upon successful connection
- **Subscription support** for updates (e.g., prices, positions, accounts)
- **Built-in performance metrics** for Graphana/Prometheus
- **Low-latency data delivery** via persistent TCP sessions

### Supported Features:

- Retrieval of trading groups, instruments, profiles, accounts, positions, orders, prices, and more
- Real-time update broadcasting to all active client sessions
- Native support for **Prometheus metrics**: request tracking and event processing

* [General messages structure](general-messages-structure.md)
* [Server message spec](server-message-spec.md)
* [Client Messages spec](client-messages-spec.md)
* [Root Models](root-models.md)
* [Calculations](calculations.md)
