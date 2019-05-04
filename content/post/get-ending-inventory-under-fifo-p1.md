---
title: "Getting Ending Inventory Under FIFO - Part 1"
linktitle: "Getting Ending Inventory Under FIFO - Part 1"
date: 2019-02-02
tags: [
    "fifo.costing",
    "custom.functions",
    "automation",
]
archives: ["2019"]
prev: /post/extracting-trial-balance-from-ledger-p3/
next: /post/get-ending-inventory-under-fifo-p2/
---
## Getting Ending Inventory Under FIFO - Part 1
<br>
There are times where in you just want to get the ending inventory amounts under a FIFO cost flow assumption. Maybe the purchase prices have been increasing for the past periods and it's logical to assume that our ending inventory consists of the higher priced materials.

Power Query can help on this.

### Introduction to Inventory Costing: Moving Average vs FIFO
FIFO means *"First-in, First-out"*. Under this method, the assumption is that ending inventory consists of the items that we're recently purchased items and Cost of Goods Sold (COGS) consists of the earlier items.

Moving average method requires us that we compute an "average cost" everytime that there's an inventory purchase. This averaging approach is considered to yield a safe and conservative approach to reporting financial results.

Moving average is typically the method most used in accounting system. The reason is that this is easier to implement in SQL that its FIFO counterpart.

We could have just one table for the moving average inventory calculation and from this, get the ending inventory and COGS as of a certain date. Moreover, moving average amounts does not differ much from the FIFO amounts and hence the use of it is often justified.


However, FIFO still offers some advantages. It often reflects the actual inventory management process in businesses. That is we often want to sell first the items that came first to avoid any spoilage.

In addition, it might be better reflect the correct net income especially in periods where there's rapid changes in acquisition cost of inventory.


### The Process
You could download the files used in this tutorial from this **[repo](https://github.com/PowerQueryforAccountants/Getting-Ending-Inventory-Under-FIFO)**.

Open **stock take.xlsx**. This contains sample inventory transactions data that we could play with.

The process of computing FIFO inventory consists of the following steps

1. Calculate **final count of the items by product_id**. 

2. Create another table with a **reverse running total of purchases** to figure out which transactions are responsible for the remaining items. A reverse running total is used because the purchases at the end are responsible for the remaining stock.

3. Merge the **final count by product_id** and the **reverse running total of purchases** tables.

4. Get the items that will consists our ending inventory using a conditional column. For the rows where

    * **final count => reverse running total, the ending inventory are the items that just came in (qty_in).**
    * **final count < reverse running total, the ending inventory is the excess of final over the previous reverse running total.**

5. Finally, **multiply the derive ending inventory by the unit cost** to get the cost of ending inventory.

Let's go over each steps one by one.

### Get the Final Count of Items

1. Create a new workbook named **fifo costing.xlsx**. Then create a new query by going to **Data tab > New Query > Workbook**. Name the query **Ending Inventory**
2. Create a custom column **NetQty** which is the difference between **qty_in** and **qty_out** columns.
3. Sort the table by the **product_id** and **document_date** columns in ascending column. This will ensure that we have the transactions in chrononological order by the **product_id**.
4. Let's create the column for the running ending balance inventory. Before we do that, copy the following code in a blank query and named it **fnRunningTotal**
    
    ```
    let
        Source = (Input as table,col as text) =>

    let
        added_Index = Table.AddIndexColumn(Input, "Index", 1, 1),
        cumulativ_total = Table.AddColumn(added_Index, "Total", 
                                            each List.Sum(
                                                List.Range(Table.Column(added_Index,col),0,[Index])
                                                        )
                                            ),
        extract_total = cumulativ_total[Total]
    in
        extract_total
    in
        Source
    ```
    
    Go back to the **Ending Inventory** query and open it. The code should look like this:

    ```
    let
        Source = Excel.Workbook(File.Contents("F:\PowerQueryforAccountants\Getting Ending Inventory Under FIFO\stock take.xlsx"), null, true),
        #"Sheet1" = Source{[Item="Sheet1",Kind="Sheet"]}[Data],
        #"Promoted Headers" = Table.PromoteHeaders(#"Sheet1", [PromoteAllScalars=true]),
        #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"document_type", type text}, {"document_date", type datetime}, {"product_id", Int64.Type}, {"qty_out", Int64.Type}, {"qty_in", Int64.Type}, {"unit_cost", type number}, {"unit_price", type number}, {"extended_cost", type number}, {"Column9", type any}}),
        #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"Column9"}),
        #"Added Custom" = Table.AddColumn(#"Removed Columns", "NetQty", each [qty_in]-[qty_out]),
        Sorting = Table.Sort(#"Added Custom",{{"product_id", Order.Ascending},{"document_date", Order.Ascending}})
    in
        Sorting
    ```

    Copy the following code and paste it after the last query code.
    ```
    #"Grouped Rows" = Table.Group(Sorting, {"product_id"}, {{"Data",

    // Function Call
    (Input as table) as table => // data for function

    let

    Call_Function = fnRunningTotal(Input,"NetQty"),
    // End of Function Call

    // Further transform
    result = Table.FromColumns(// putting all together
        Table.ToColumns(Input)&{Call_Function},
        Value.Type(Table.AddColumn(Input, "Total", each null, type number))
        )
    in
    result,


    type table}})
    ```

    The **Ending Inventory** query should now look like this.
    ```
    let
        Source = Excel.Workbook(File.Contents("F:\PowerQueryforAccountants\Getting Ending Inventory Under FIFO\stock take.xlsx"), null, true),
        #"Sheet1" = Source{[Item="Sheet1",Kind="Sheet"]}[Data],
        #"Promoted Headers" = Table.PromoteHeaders(#"Sheet1", [PromoteAllScalars=true]),
        #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"document_type", type text}, {"document_date", type datetime}, {"product_id", Int64.Type}, {"qty_out", Int64.Type}, {"qty_in", Int64.Type}, {"unit_cost", type number}, {"unit_price", type number}, {"extended_cost", type number}, {"Column9", type any}}),
        #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"Column9"}),
        #"Added Custom" = Table.AddColumn(#"Removed Columns", "NetQty", each [qty_in]-[qty_out]),
        Sorting = Table.Sort(#"Added Custom",{{"product_id", Order.Ascending},{"document_date", Order.Ascending}}),
        #"Grouped Rows" = Table.Group(Sorting, {"product_id"}, {{"Data",

        // Function Call
        (Input as table) as table => // data for function

        let

        Call_Function = fnRunningTotal(Input,"NetQty"),
        // End of Function Call

        // Further transform
        result = Table.FromColumns(// putting all together
            Table.ToColumns(Input)&{Call_Function},
            Value.Type(Table.AddColumn(Input, "Total", each null, type number))
            )
        in
        result,


        type table}})
    in
        #"Grouped Rows"
    ```

    Don't worry if you don't understand the code for now. What it does is simply get a running ending quantity by each **product_id**

    The table should now look like this.

    ![GroupedRows](/img/get-ending-inventory-under-fifo-p1/grouped_rows.png)
    <br/>
    <br/>
    
5. Expand the Data column. The table should now look like this
    
    ![ExpandedData](/img/get-ending-inventory-under-fifo-p1/expanded_data.png)
    
6. Let now grouped again our data to get the ending inventory as of the last date for each product. Go to **Transform tab > Group By**.

    Input the following

    ![Groupby](/img/get-ending-inventory-under-fifo-p1/groupby.png)
    <br/>
    <br/>

    The **Sum** operation will not give the ending inventory but we're going to use it as a scaffold to our code.
    
    After doing that, open the query in the **Advanced Editor** and replace the **List.Sum** with **List.Last**. This will give us the last row for each product which contains the ending inventory.

    The data should now look like this.

    ![Ending Inventory](/img/get-ending-inventory-under-fifo-p1/ending_inventory.PNG)
    <br>

### Conclusion
That's it for now. In the next tutorial, we're going to create the reverse running total of the purchases and merge this to our **Ending Inventory** table. Click on this link to view **[Part 2](../get-ending-inventory-under-fifo-p2/)** of this tutorial.

**Stay Querious. Happy coding!**
