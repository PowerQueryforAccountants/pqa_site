---
draft: true
title: "Creating a Simple Accounting System with Excel and Power Query - Part 3"
linktitle: "Creating a Simple Accounting System with Excel and Power Query - Part 3"
date: 2019-10-05
<!-- draft: true -->
tags: [
    "accounting.system",
    "data.validation",
    "named.range",
    "general.ledger",
    "trial.balance",
    "balance.sheet",
    "income.statement"
]
archives: ["2019"]
prev: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p2/
next: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p3/
---

## Creating a Simple Accounting System with Excel & Power Query - Part 3
<br>

This is the **Part 3** of the series **Creating a Simple Accounting System with Excel & Power Query**.

If you missed previous tutorials, you could view them on the following links

**[Creating a Simple Accounting System with Excel & Power Query - Part 1](../creating-a-simple-accounting-system-with-excel-and-power-query-p1/)**
<br>
**[Creating a Simple Accounting System with Excel & Power Query - Part 2](../creating-a-simple-accounting-system-with-excel-and-power-query-p2/)**

### Recap of our Accounting System
In the previous post, we have created the Trial Balance and Profit & Loss statements for our accounting system. In this post, we're going to complete basic reports by doing the Balance Sheet.

### The Balance Sheet Report
The **Balance Sheet** shows all the resources of a business (the **Assets**) and how these were financed, either thru debt (the **Liabilities**) or thru owner contributions (the **Equity**).

Together, the equation **Assets = Liabilities + Equity** should hold true when creating the Balance Sheet. This is the **Fundamental Accounting Equation** and it has been the guiding equation for accountants for years now (as as long as accounting exists!). If you've done the TB in the previous section, this should be easy.

1. Create a blank query again and get the data from the **4_TB** query. 
2. Filter the Account Type to include only the Assets, Liabilities, and Equity account types.
    
    ![Final BS](/img/creating-a-simple-accounting-system-with-excel-and-power-query/filter_bs.png)

3. Remove the Debit and Credit columns. At this point, our Balance Sheet should look like this.
    
    ![Preliminary BS](/img/creating-a-simple-accounting-system-with-excel-and-power-query/preliminary_bs.png)

    The Balance column should total to zero as a result of the Fundamental Accounting Equation. However, this is not the case. The total of Liabilities and Equity is 23.26 more than the assets (223.26 vs 200 - in absolute amounts.).

    The reason for this is that the **Net Loss** is not yet added to the Equity and hence Equity side is overstated. We have to combine the resulting income/loss first to our Equity and then proceed with the final Balance Sheet.

    Rename this query as **6_BS-before NI**. Leave it for now and let's compute for the Net Income/Loss

### Computing the Net Income/Loss
To get the Net Income/Loss, we have to create another query for this. Take note that this query will report the *Net Loss as positive (having a debit balance) and Net Income as negative (having a credit balance)*. 

1. As start, create a blank query again and get the data from the **4_TB** query. 
2. Filter the Account Type to include only the Revenue and Expenses account types.
3. Remove the Debit and Credit columns. The query should now look like this.

    ![Net Income Loss](/img/creating-a-simple-accounting-system-with-excel-and-power-query/net_inc_loss1.png)
    <br>
    <br>

4. To combine the amounts, add a custom column with the string "Net Income/Loss"
    
    ![Custom Column Net Income Loss](/img/creating-a-simple-accounting-system-with-excel-and-power-query/custom_col_net_inc_loss.png)
    <br>
    <br>

5. Now, apply the Group By function to this column as follows
    ![Group Net Income Loss](/img/creating-a-simple-accounting-system-with-excel-and-power-query/group_net_inc_loss.png)

    The query should now look like this.
    
    ![Net Income Loss](/img/creating-a-simple-accounting-system-with-excel-and-power-query/net_inc_loss2.png)
    <br>
    <br>

    It is a Net Loss. Its sign (positive) is opposite from the P&L created above (negative) as that one is used for reporting purpose. The amount we compute here is will be used only to create our final Balance Sheet and not presented separately in the workbook.

6. Now, we have to append this to our preliminary Balance Sheet above. But before we do that, some housekeeping first. We have to change this query with same format as the Balance Sheet. That means:
    * Create Account # and Account Type columns with null values.
    * Rename columns: Amount to Balance, and the Total to Account
    * Rearrange columns: Same order with the Balance Sheet query

    It should now look like this

    ![Net Income Loss Final](/img/creating-a-simple-accounting-system-with-excel-and-power-query/net_inc_loss_final.png)
    <br>
    <br>
    
    Rename this query as **7_P&L_NI**. 

### Balance Sheet Revisited
Finally, we're ready to complete our Balance Sheet.

1. Create an Append query

    ![Append](/img/creating-a-simple-accounting-system-with-excel-and-power-query/append.png)

2. Append the Net Loss at the end of the Balance Sheet.
    
    ![Append Queries](/img/creating-a-simple-accounting-system-with-excel-and-power-query/append_queries.png)

    The query should now look like this.

    ![Final BS](/img/creating-a-simple-accounting-system-with-excel-and-power-query/final_bs.png)

    Upon checking, the accounting holds true and the Balance column totals to zero. Rename the final balance sheet query as **8_BS**. 


### Conclusion
In this tutorial, we have completed the basic reports needed for our accounting system. In the next post, we're going to load reports, and do some finishing touches to our system. 

Till then, **Stay Querious & Happy coding!**


