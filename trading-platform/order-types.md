# Order Types

Our platform supports the following order types:

## Market Orders

- Executes immediately at the current market price.
- **Use Case**: Ideal for fast-moving markets when you need immediate execution.

## Limit Orders

- Executes only when the market reaches a specified price or better.
- If the price set in a limit order is worse than the current market price, the order will execute as a market order.
- **Use Case**: Useful for entering or exiting trades at a specific level, ensuring better control over price.

## Stop Orders

- Triggers an order when the market reaches a specified price.
- **Use Case**: Often used for stop-losses or breakout trades.

## When to Use Limit vs Stop Orders

- **Limit Orders**: Use when you anticipate a price retracement before execution.
- **Stop Orders**: Use when entering on momentum or as a safeguard against losses.

## Take Profit and Stop Loss Relationship

- **Stop Loss**: A Stop Loss is essentially a **Stop Order** that triggers a market order to close a position when the market price reaches a specified level, minimizing potential losses.
- **Take Profit**: A Take Profit is essentially a **Limit Order** that closes a position when the market reaches the specified price, locking in profits.

## Pending Orders and Expiry Time

Pending orders can remain active for a specified period or until manually canceled. Our platform supports the following time validity settings:

1. **Good Till Cancelled (GTC)**:
      - The order remains active indefinitely until it is manually canceled by the trader.
      - Ideal for traders who want their order to persist regardless of time constraints.
2. **Good Till Date/Time (GTD)**:
      - The order remains active until a specified date and time.
      - Useful for traders who want their order to expire automatically after a specific period, aligning with their strategy.

These time validity options give traders complete control over their pending orders, ensuring flexibility and precision in execution.