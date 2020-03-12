---
draft: true
title: "Getting Ending Inventory Under FIFO - Part 2"
linktitle: "Getting Ending Inventory Under FIFO - Part 2"
date: 2019-02-09
tags: [
    "fifo.costing",
    "custom.functions",
    "automation",
]
archives: ["2019"]
prev: /post/get-ending-inventory-under-fifo-p1/
next: /post/get-ending-inventory-under-fifo-p3/
---
## Getting Ending Inventory Under FIFO - Part 2
<br>

This is the **Part 2** of the series **Getting Ending Inventory Under FIFO**.
If you missed the **Part 1**, you can read it **[here](../get-ending-inventory-under-fifo-p1/)**

### Create Reverse Running Total of Purchases
After creating the **Ending Inventory** table, we have to create a reverse running total our purchases. A reverse running total is used because the purchases at the end are responsible for the remaining stock.

1. In the **fifo costing.xlsx** workbook, create another query and named it **ReverseRunningTotal**
2. Filter the **qty_in** where items is not equal to 0.
3. Sort the table by **product_id** in ascending order and by **document_date** in descending order. This will make the beginning items to be at the last transaction for each **product_id**
3. Let's now create the reverse running total column.
    Open the **ReverseRunningTotal** query and past the following code at the end.

    ```
    #"Grouped Rows" = Table.Group(#"Sorted Rows", {"product_id"}, {{"Data",

    // Function Call
    (Input as table) as table => // data for function

    let

    Call_Function = fnRunningTotal(Input, "qty_in"),
    // End of Function Call

    // Further transform
    result = Table.FromColumns(// putting all together
        Table.ToColumns(Input)&{Call_Function},
        Value.Type(Table.AddColumn(Input, "RevRunningTotal", each null, type number))
        )
    in
    result,


    type table}}),
    ```

4. Expand the resulting **Data** column. The table should now look like this

    ![ReverseRunningTotal](/img/get-ending-inventory-under-fifo-p2/revrunningtotal.png)

We now have the **ReverseRunningTotal** table. The next step is to merge this table to our **Ending Inventory** table.

### Merge ReverseRunningTotal with the Ending Inventory table
By merging the tables, we could now get a single table that shows the ending inventory by product and the reverse running total of purchases. With this setup, we can compare the two quantities to get the items that comprise the ending inventory.

1. Right-click on the Queries Pane and choose Combine > Merge.
    
    ![MergeQuery](/img/get-ending-inventory-under-fifo-p2/merge_query.PNG)
    <br/>
    <br/>

2. Input the following. Make sure that we're combining on the product_id field (highlighted)
    ![Merge](/img/get-ending-inventory-under-fifo-p2/merge.PNG)
    <br/>
    <br/>
    
3. Expand the resulting **Ending Inventory** column. Include only the **Count** and untick the **Use original column name as prefix**.

    The resulting table should now look like this
    ![Merge Table](/img/get-ending-inventory-under-fifo-p2/merge_table.PNG)

    **Something is wrong!!!**

    The **RevRunningTotal** is now different from the original one that we computed. In fact, our table is now resorted and the beginning inventory appears first.

    To solve this, wrap the `Source` step with `Table.Buffer()` when expanding the table column. The code is as follows

    ```
    Table.ExpandTableColumn(Table.Buffer(Source), "Ending Inventory", {"Count"}, {"Count"})
    ```
    
    Now, the table is back to the original **ReverseRunningTotal** table and is the result that we want.

    ![Merge Table 2](/img/get-ending-inventory-under-fifo-p2/merge_table2.PNG)


### Conclusion
That's it for now. In the next and last part of this series, we're going to finalize our code and get the final inventory. Click on this link to view **[Part 3](../get-ending-inventory-under-fifo-p3/)** of this tutorial.

**Stay Querious. Happy coding!**