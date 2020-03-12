---
draft: true
title: "Getting Ending Inventory Under FIFO - Part 3"
linktitle: "Getting Ending Inventory Under FIFO - Part 3"
date: 2019-02-23
tags: [
    "fifo.costing",
    "custom.functions",
    "automation",
]
archives: ["2019"]
prev: /post/get-ending-inventory-under-fifo-p2/
next: /post/detecting-unrecorded-sales/
---
## Getting Ending Inventory Under FIFO - Part 3
<br>

This is the **Part 3** and last installment of the series **Getting Ending Inventory Under FIFO**.

You could view the previous tutorials on the following links

**[Getting Ending Inventory Under FIFO - Part 1](/post/get-ending-inventory-under-fifo-p1/)**
<br>
**[Getting Ending Inventory Under FIFO - Part 2](/post/get-ending-inventory-under-fifo-p2/)**

With that said, let's get started.

### Get the Ending Inventory
At this point, we now have the necessary information to compute our ending inventory.

In the merged table, we have to determine the row where the **final count first becomes less than the reverse running total**. Anything before this row, the ending inventory is comprise of the **qty_in** column. 

However, upon reaching this row, the items to complete the ending inventory is the excess of final count over the RevRunningTotal in the previous row.

For example, the following portion for **Product 52**.

![Product 2](/img/get-ending-inventory-under-fifo-p3/product_52.PNG)

The ending inventory is consists as follows

* 50pcs
* 600pcs
* 1560pcs
* **80pcs** - The row wherein the final count becomes less than the reverse running total is the row where RevRunningTotal is equal to 2810. The remaining items to be filled is only 80 pcs at this point (2,290 less the 2,210 from the preceding row).

With that said, let's implement this in Power Query.

1. Create a blank query and paste the following code. Name the query as **fnPreviousRow**.

    We're going to use this query to refer to previous row of transaction. If you want to learn more about this function, you can check out this great blog post [here](https://www.thebiccountant.com/2018/07/12/fast-and-easy-way-to-reference-previous-or-next-rows-in-power-query-or-power-bi/).

    ```
    let func =   
     (Table as table, optional Step as number, optional SelectedColumns, optional GroupByColumns, optional Suffix as text, optional Buffer as any) =>


    let
    // Steps to prepare the (optional) parameters for the nested function "fnFetchNextRow"
        Source = if Buffer = null then Table else Table.Buffer(Table),
        Step0 = if Step = null then -1 else Step,
        Step_ = if Step = null then 1 else Number.Abs(Step),
        Suffix = if Suffix = null then ".Prev" else Suffix,
        GroupByColumns = if GroupByColumns = null then null else GroupByColumns,
        ShiftFunction = if Step0 < 0 then Table.RemoveLastN else Table.RemoveFirstN,
        ColNames = List.Buffer(Table.ColumnNames(Source)),
        NewColNames = if SelectedColumns = null then ColNames else SelectedColumns,
        CountNewCols = List.Count(NewColNames),


    // Core function that retrieves values from previous or next rows (depending on sign of parameter "Step")
        fnFetchNextRow = (Table_ as table, optional Step as number, optional SelectedColumns, optional Suffix as text, optional Buffer as any) =>
            let
                MergeTable = if SelectedColumns = null then Table_ else Table.SelectColumns(Table_, SelectedColumns),
                Shift = if Step0 > 0 then ShiftFunction(MergeTable, Step_) & #table(NewColNames, List.Repeat({List.Repeat({null}, CountNewCols)}, Step_))
                                    else #table(NewColNames, List.Repeat({List.Repeat({null}, CountNewCols)}, Step_)) & ShiftFunction(MergeTable, Step_),
                Reassemble = Table.ToColumns(Table_) & Table.ToColumns(Shift), 
                Custom1 = Table.FromColumns( Reassemble,  Table.ColumnNames(Source) & List.Transform(NewColNames, each _&Suffix ) )
            in
                Custom1,


    // optional grouping on certain columns
        #"Grouped Rows" = Table.Group(Source, GroupByColumns, {{"All", each _}}, GroupKind.Local),
        #"Added Custom" = Table.AddColumn(#"Grouped Rows", "Custom", each fnFetchNextRow([All], Step0, SelectedColumns, Suffix, Buffer)),
        #"Removed Columns" = Table.Combine(Table.RemoveColumns(#"Added Custom", GroupByColumns & {"All"})[Custom]),


    // case no grouping
        NoGroup = fnFetchNextRow(Source, Step0, SelectedColumns, Suffix, Buffer),


    // select case grouping
        Result = if GroupByColumns = null then NoGroup else #"Removed Columns"
    in
        Result ,
    documentation = [
    Documentation.Name =  " Table.ReferenceDifferentRow ",
    Documentation.Description = " Adds columns to a <code>Table</code> with values from previous or next rows (according to the <code>Step</code>-index in the 2nd parameter) ",
    Documentation.LongDescription = " Adds columns to a <code>Table</code> with values from previous or next rows (according to the <code>Step</code>-index in the 2nd parameter) ",
    Documentation.Category = " Table ",
    Documentation.Source = "  ",
    Documentation.Version = " 1.0 ",
    Documentation.Author = " Imke Feldmann (www.TheBIccountant.com ) ",
    Documentation.Examples = {[Description =  "  ",
    Code = " Table.ReferenceDifferentRow( #table( {""Product"", ""Value""}, List.Zip( { {""A"" ,""A"" ,""B"" ,""B"" ,""B""}, {""1"" ,""2"" ,""3"" ,""4"" ,""5""} } ) ) ) ",
    Result = " #table( {""Product"", ""Value"", ""Product.Prev"", ""Value.Prev""}, List.Zip( { {""A"" ,""A"" ,""B"" ,""B"" ,""B""}, {""1"" ,""2"" ,""3"" ,""4"" ,""5""}, {null ,""A"" ,""A"" ,""B"" ,""B""}, {null ,""1"" ,""2"" ,""3"" ,""4""} } ) ) "]}]
      
     in  
      Value.ReplaceType(func, Value.ReplaceMetadata(Value.Type(func), documentation))
    ```

2. Open the merged table and paste the following code at the end.
    ```
    Custom1 = fnPreviousRow(#"Expanded Ending Inventory",null,{"RevRunningTotal"})
    ```

    The query should now look like this
    ```
    let
        Source = Table.NestedJoin(ReverseRunningTotal,{"product_id"},#"Ending Inventory",{"product_id"},"Ending Inventory",JoinKind.LeftOuter),
        #"Expanded Ending Inventory" = Table.ExpandTableColumn(Table.Buffer(Source), "Ending Inventory", {"Count"}, {"Count"}),
        Custom1 = fnPreviousRow(#"Expanded Ending Inventory",null,{"RevRunningTotal"})
    in
        Custom1
    ```

    You now have a column referencing the **previous RevRunningTotal**.

3. Create a conditional column that will contain our ending inventory. Let's name the column **Inventory**.
    
    ![Inventory](/img/get-ending-inventory-under-fifo-p3/inventory.png)
    <br/>
    <br/>

    The last condition will not give us the remaining items of inventory but we'll use this as scaffold to our code.

    Now, open the query in **Advanced Editor**. Edit the code from this

    ```
    Table.AddColumn(Custom1, "Inventory", each if [Count] >= [RevRunningTotal] then [qty_in] else if [Count] < [RevRunningTotal] then [RevRunningTotal.Prev] else null)
    ```

    To this
    ```
    Table.AddColumn(Custom1, "Inventory", each if [Count] >= [RevRunningTotal] then [qty_in] else if [Count] < [RevRunningTotal] then [Count]-[RevRunningTotal.Prev] else null)
    ```

    The table now looks like this

    ![Ending Inventory](/img/get-ending-inventory-under-fifo-p3/end_invty1.PNG)
    <br/>
    <br/>

4. Filter the **Inventory** column to exclude negative amounts

    ![Filtered Inventory](/img/get-ending-inventory-under-fifo-p3/end_invty2.PNG)
    <br/>
    <br/>

5. Finally, create a column to multiple the **Inventory** column by the **unit_cost** to get the **Ending Inventory Cost**.
    Delete any unused columns as necessary.

    ![Inventory Cost](/img/get-ending-inventory-under-fifo-p3/end_invty3.PNG)
    <br/>
    <br/>
    
    **Whew!** Finally we're done. We now have the **Ending Inventory, in units and amount**.

### Conclusion
In this series, we have learn how to use Power Query to get the FIFO inventory from inventory transactions listing. We have learn how to use different Power Query features such as the **Merge** and **Group By**.

With the right imagination, we could automate tasks that usually will take a lot of time with the native Excel alone.

That's it for now. Check out other post here to learn more about Power Query.

**Stay Querious. Happy coding!**