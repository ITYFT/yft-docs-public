# How To

## How to Open a Position

1. Navigate to the trading interface and select the symbol you want to trade.
2. Specify the trade parameters:
      - Choose the position size (e.g., number of lots).
      - Optionally, set a Take Profit (TP) and Stop Loss (SL) level.
3. Click on the **Buy** or **Sell** button based on your market direction prediction.
4. Review the confirmation message to ensure the trade has been successfully placed.

---

## How to Modify Position TP/SL

1. Open the **Open Positions** section or click on the position label directly on the chart.
2. Select the position you want to modify.
3. Update the Take Profit (TP) and/or Stop Loss (SL) values in the trade modification form.
4. Click the **Save** or **Modify** button to apply the changes.
5. A confirmation message will appear, and the updated TP/SL will be displayed.

---

## How to Reverse a Position

1. Navigate to the **Open Positions** section. Expand the group if the position is grouped by symbol.
2. Click the **Reverse Position** button in the position's **Action** column.
3. The dialog shows the switch you are about to make, for example `SELL 0.12 → BUY 0.12 lots`, together with the profit or loss the close will realise and the total cost.
4. Adjust **Size** if you want to flip into a smaller position than the one you hold.
5. Click **Reverse**.
6. The current position is closed and a new position is opened in the opposite direction. The new position has its own ID — it is a new position, not the old one turned around.

To skip the dialog entirely, switch on **One click reverse position** under
**Settings → Trade**.

---

## How to Reverse a Position Including TP/SL

1. Open the **Reverse position** dialog as above.
2. Set **Protection** for the new position:
   - **1R : 2R** applies a ready-made one-to-two risk-reward pair.
   - **Custom** lets you enter Stop Loss and Take Profit yourself — by Price, Points, PnL or Equity % — and switch on **Trailing SL** if you want the new position to trail.
3. Click **Reverse**.

To have the current Take Profit and Stop Loss carried over and flipped automatically every
time, switch on **Copy TP/SL on reverse** under **Settings → Trade**. The levels are
mirrored for the new direction, so the original risk-reward ratio is preserved.

---

## How to Partially Close a Position

1. Navigate to the **Open Positions** section and expand the group if needed.
2. Click the **Partial Close** button in the position's **Action** column.
3. Enter the lots to close, or use the 0 / 25 / 50 / 75 / 100% slider.
4. Check **Est. Realized P&L** — it breaks the result into Gross, allocated Swap and Commission — along with **Remaining after close** and **Est. Margin freed**.
5. Click **Close N lots**.

The rest of the position stays open under the **same position ID** and the same open
price. The booked result appears in the **Realized PnL** column of the positions table.

You cannot close less than the instrument's minimum trade size, and you cannot leave a
remainder below that minimum. If a position is too small to split, close it in full.

Use **Settings → Trade → Partial close** to switch the dialog between the extended
breakdown and a compact view.

---

## How to Set a Trailing Stop Loss

1. In the trade form, switch on **Trail**. **Stop Loss** is activated with it — a trailing stop always needs one.
2. Set the Stop Loss by Price, Points, PnL or Equity %. The gap between it and the current price is the distance the stop will keep.
3. Place the trade, or save the change if you are editing an open position.

From then on the stop follows the market in your favour — up for a Buy, down for a Sell —
holding that distance, and stays put when price moves against you. It never moves
backwards.

To attach a trailing stop to an existing position, open it for editing and switch on
**Trail** there. To turn it off, switch **Trail** off; the Stop Loss stays where it last
moved to.

---

## How to Close a Position

1. Go to the **Open Positions** section in the trading interface.
2. Select the position you want to close.
3. Click the **Close** button next to the position.
4. Confirm the closure in the pop-up dialog.
5. The position will be closed, and the realized profit or loss will be reflected in your account balance.

---

## How to Open a Limit Order

1. Select the symbol you want to trade.
2. In the order form, choose the **Limit Order** option.
3. Enter the desired price at which you want the order to execute.
4. Specify the position size and optionally set TP/SL levels.
5. Click on **Place Order** to submit the limit order.
6. The order will appear in the **Pending Orders** section until the specified price is reached.

---

## How to Open a Stop Order

1. Select the symbol you want to trade.
2. In the order form, choose the **Stop Order** option.
3. Enter the trigger price at which the order will activate.
4. Specify the position size and optionally set TP/SL levels.
5. Click on **Place Order** to submit the stop order.
6. The order will remain in the **Pending Orders** section until the trigger price is reached.

---

## How to Set Up Take Profit

1. While placing an order, locate the **Take Profit (TP)** field in the order form.
2. Enter the price level at which you want the position to close automatically to secure profits.
3. For existing positions, go to the **Open Positions** section or chart, select the position, and modify the TP value.
4. Confirm the changes to activate the Take Profit.

---

## How to Set Up Stop Loss

1. While placing an order, locate the **Stop Loss (SL)** field in the order form.
2. Enter the price level at which you want the position to close automatically to minimize losses.
3. For existing positions, go to the **Open Positions** section or chart, select the position, and modify the SL value.
4. Confirm the changes to activate the Stop Loss.