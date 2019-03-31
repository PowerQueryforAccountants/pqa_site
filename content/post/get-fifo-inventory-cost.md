---
title: "Getting FIFO Inventory Cost"
linktitle: "Getting FIFO Inventory Cost"
date: 2019-02-09
draft: true
tags: [
    "groupby",
    "fifo costing",
    "custom functions"
]
---
archives: ["2019"]
### Getting FIFO Inventory Cost
<br>

### Introduction to Inventory Costing: Moving Average vs FIFO
Moving average is typically the method most used in accounting system. The reason is that this is easier to implement in sql that its fifo counterpart.

We could have just one table for the moving average inventory calculation from this, get the ending inventory and cogs as of a certain date. For it's fifo counterpart, 

Moreover, moving average amounts does not differ much from the fifo amounts and hence the use of it is often justified.


However, fifo still offers some advantages. It often reflects the actual inventory management process it stores. That is we often want to sell first the items that came first.

In addition, it might be better reflect the correct net income especially in periods where there's rapid changes in cost of inventory.


### The process
Open stock_take.xlsx. This contains sample inventory transactions data that we could play with.

The process of computing fifo inventory consists of the following steps
1. Calculate final count of the items by product_id. 

2. Create another table with a reverse running total of purchases to figure out which transactions are responsible for the remaining items. A reverse running total is used because the purchases at the end are responsible for the remaining stock.

3. Merge the final count by product_id and the table with reverse running total. By now, you should have the following table.

4. We're going to determine the row where the final count first becomes less than the reverse running total. This is the row where we have to stop in getting the remaining stock. In this row, we have to determine the excess of the final count from the previous reverse running total value.

Hence, create another column referencing previous values of the reverse running total.

5. At this stage, we can now get the items that will consists our ending inventory. Create a conditional column for this. For the rows where Count is more than the reverse running total, the ending inventory are the items that just came in (qty_in). For the rows where Count < reverse running total, the ending inventory is the excess of Count over the previous reverse running total. In Power Query, it looks like this

TODO: image

6. In the conditional column, filter only positive amounts. The rows where it is negative means that we already satisfied our ending inventory by tracing back the qty in. 

7. Finally, Multiply the derive ending inventory by the unit cost to get the cost of ending inventory.

### Bonus: Getting the COGS
Getting the cost of goods sold works in the same process. This time, we use the running total of the qty_out instead. The logic is that COGS consists of the items that first arrive in the inventory queue.

I'll leave this as exercise to you.

### Conclusion