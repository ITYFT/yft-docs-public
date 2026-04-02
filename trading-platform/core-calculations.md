# Core Calculations

## Realized PnL (Closed Positions):

1. **For a Buy Position**:
      - Formula:
        ```
        Realized PnL = (Close Price - Open Price) x Position Size x Contract Size
        ```
      - When closing a buy position, profit is made if the close price is higher than the open price. The difference is multiplied by the position size and the contract size to determine total profit or loss.
2. **For a Sell Position**:
      - Formula:
        ```
        Realized PnL = (Open Price - Close Price) x Position Size x Contract Size
        ```
      - When closing a sell position, profit is made if the close price is lower than the open price. The difference is multiplied by the position size and the contract size.

## Unrealized PnL (Open Positions):

1. **For a Buy Position**:
      - Formula:
        ```
        Unrealized PnL = (Current Price - Open Price) x Position Size x Contract Size
        ```
      - For open buy positions, unrealized profit is calculated as the difference between the current price and the open price. If the current price is higher, the position is in profit.
2. **For a Sell Position**:
      - Formula:
        ```
        Unrealized PnL = (Open Price - Current Price) x Position Size x Contract Size
        ```
      - For open sell positions, unrealized profit is calculated as the difference between the open price and the current price. If the current price is lower, the position is in profit.

## Equity

- Formula:
  ```
  Equity = Balance + Unrealized PnL
  ```
- Equity represents the total value of a trader’s account, combining the account balance and the unrealized profit or loss from open positions. It reflects the current value of your trading account in real time.

## Margin

- Formula:
  ```
  Margin = (Position Size x Contract Size) / Leverage
  ```
- The margin required to open a position is calculated based on the position size, the standardized contract size, and the leverage. Margin represents the amount of capital needed to maintain a trade.
    - **Position Size**: Number of lots traded.
    - **Contract Size**: Standardized value per lot (e.g., 100,000 for forex).
    - **Leverage**: Ratio of borrowed funds to trader’s capital.

## Used Margin

- The portion of your account funds locked to maintain open positions.
- Used margin increases with the number of open positions and their size, directly impacting your available free margin.

## Free Margin

- Formula:
  ```
  Free Margin = Equity - Used Margin
  ```
- Free margin is the amount of funds available for opening new positions or absorbing losses. It provides flexibility and ensures account health.

## Margin Level

- Formula:
  ```
  Margin Level = (Equity / Used Margin) x 100
  ```
- Margin Level is a critical metric that measures the health of your account. It is expressed as a percentage and helps assess how much margin is available relative to your open positions. A higher margin level indicates a safer buffer, while a lower margin level suggests higher risk of liquidation.

### Additional Notes

When there is no direct exchange rate available for a currency pair, the platform ensures accurate calculations by using alternative methods:

1. **Inverse Rate**: If a direct rate (e.g., EUR/USD) is unavailable, the platform automatically calculates the exchange rate using the inverse of the related rate (e.g., USD/EUR = 1 / EUR/USD).
2. **Cross Rate**: If neither a direct nor inverse rate exists, the platform calculates a cross rate by combining two related currency pairs. For example, if EUR/JPY is unavailable, it can be derived by multiplying EUR/USD and USD/JPY:

```
EUR/JPY = EUR/USD × USD/JPY
```

These mechanisms ensure seamless trading across all currency pairs, maintaining precision and reliability even for uncommon combinations.