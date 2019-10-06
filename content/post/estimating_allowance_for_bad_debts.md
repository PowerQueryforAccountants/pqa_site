---
title: "Estimating Allowance for Bad Debts"
linktitle: "Estimating Allowance for Bad Debts"
date: 2019-10-05 01:00:00
draft: true
tags: [
    "power.query"
]
archives: ["2019"]
prev: /post/power_query_syntax_highlighter/
next: /post/sales_trend_analysis/
---


## Estimating Allowance for Bad Debts
<br>
In this post, we're going to discuss how to estimate the bad debts from a list of receivables. You could download the files [here](link). Let's dive right in.

### Grouping Receivables according to Days Due

The first step is to get the days outstanding for each invoice. There are two ways to do it
1. Get the difference between the Invoice Date and Today's date.
2. Get the difference between the Invoice Due Date and Today's date.

The 2nd one will be shorter than the first one cause we're going to consider the invoice terms in the 2nd option. We're going to use the first one as it is a better measure of the collectability of the receivable. Money tied up in receivable is not usable and a business should strive to lessen this for it to remain healthy. We're going to use the 1st one in this example.

The steps are
1. Create a query `ARTransactions` and load the `ReceivablesT` table.

2. Add a new column `Today` for the today's date using `DateTime.LocalNow()`. Format the new column to include the Date only by going to Transform > Date > Date Only.

3. Add a new column `DateDifference` to get the difference between the invoice date and today's date.

4. Now, we need to define a function to group the DateDifference to appropriate bands. Copy the following function to a new query
    
    ```
    (days) => 
      let ARBand = 
        { 
            {(x)=>x<31, "Current"}, 
            {(x)=>x<61, "30-60 Days"}, 
            {(x)=>x<91, "61-90 Days"},
            {(x)=>x<121, "91-120 Days"},
            {(x)=>true, ">120 Days"} 
        }, 
      Result = List.First(List.Select(ARBand, each _{0}(days))){1} 
    in 
      Result
    ```
    
    Named the new query/function `DayBanding`.

5. Add a new column `Days OS` by applying the `DayBanding` function to the `DateDifference` column.

6. Remove the columns `Transaction Date`, `Invoice #`, `Today`, and `DateDifference` to prepare our table for the the Pivot function.

7. Now, pivot the table to create new columns for the `Days OS` column and sum the `Amounts` by `Customer` column. The final table should now look like this.

TODO image

### Applying Bad Debts Percentage to AR Bands
Now, we apply the Bad debts percentage to our AR summary.

1. Copy the `ARTransactions` query and remove the pivot step to revert it back to the long format. Named the query `ARList`
2. Create a new query `BadDebtsPercentage` and load the `BadDebtsT` table.
3. Create a merge query and merge `ARList` and `BadDebtsPercentage` on the `DaysOS` and `Band` columns
4. Create a new column `Bad Debts` by multiplying the `Amount` and `HistoricalPercentages` column.
5. Remove the columns TODO
6. Now, pivot the table to create new columns for the `Days OS` column and sum the `Bad Debts` by `Customer` column. The final table should now look like this.


### Conclusion
In this post, you have learn how to use PowerQuery in order to create a quick AR aging report and estimate the bad debts amount. This is really helpful in financial analysis as it provides you a real time picture of your business' collection efficiency. 

That's it for now. In the next post, we're going to study how to create a sales trend analysis report in Power Query.

Till then, **Stay Querious & Happy coding!**