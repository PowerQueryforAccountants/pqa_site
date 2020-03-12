---
draft: true
title: "Creating a Simple Accounting System with Excel and Power Query - Part 2"
linktitle: "Creating a Simple Accounting System with Excel and Power Query - Part 2"
date: 2019-09-28
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
prev: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p1/
next: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p3/
---

## Creating a Simple Accounting System with Excel & Power Query - Part 2
<br>

This is the **Part 2** of the series **Creating a Simple Accounting System with Excel & Power Query**.
If you missed the **Part 1**, you can read it **[here](../creating-a-simple-accounting-system-with-excel-and-power-query-p1/)**


### Recap of our Accounting System
In the previous post, we have created the meat of our accounting system using tables, name ranges, and data validation. It basically has the following components:

* **Chart of Accounts**
* **Customer list**
* **Supplier list**
* **General Ledger**

In this post, we're going to incorporate Power Query in order to create reports from our accounting system that are easy to update. Our system can be only more relevant to our users if they could produce reports that they can used to make business decisions. 

### Creating the General Ledger
First, we have to recreate our **General Ledger**. Why do we have to do it? Basically, we want to add another column in our General Ledger to specify the type of each accounts. We're going to use the Account Types to easily create our PL and Balance sheet later on.

1. To start, go to the General Ledger sheet and select the **GeneralLedgerT**. Then go to **Data tab > From Table/Range** in the Get & Transform Data group.

    ![From Table/Range](/img/creating-a-simple-accounting-system-with-excel-and-power-query/from_table_range.png)
    <br>
    <br>

    Once loaded into the PowerQuery window, just leave as it is. Name the query as **1_GL**. Close and load it as connection only. We're going to use this in our merge commands later on. 
    <br>
    <br>

2. Next, load the **AccountsT** to a query too. Follow the same instructions with loading the GeneralLedgerT. Notice how are AccountsT has blank rows. This is to accomodate future account titles that we want to add. In our query though, we have to exclude them by filtering the Account Name column and remove blanks/null.

    ![Filter Account Name](/img/creating-a-simple-accounting-system-with-excel-and-power-query/filter_account_name.png)
    <br>
    <br>

    Named the query as **2_AccountsT**. Close and load it as connection only.

3. Now, we're ready to merge them the **1_GL** and **2_AccountsT**. In the PowerQuery editor, right-click on the **Queries Pane**,  **New Query > Combine > Merge Queries as New**.

    ![Merge Queries](/img/creating-a-simple-accounting-system-with-excel-and-power-query/merge_queries.png)
    <br>
    <br>

    Merge on the Account column and choose **Left Outer** in the Join Kind.

    ![Merge Queries Dialog Box](/img/creating-a-simple-accounting-system-with-excel-and-power-query/merge_queries_dialog.png)
    <br>
    <br>

4. Expand the resulting column and include only Account # and Account Type.

    ![Expand Column](/img/creating-a-simple-accounting-system-with-excel-and-power-query/expand_col.png)

    Named this query as **3_GL complete**. I choose to not prefix the columns with the original column name cause there are NO conflicts with the other column names. After doing that, we now have the complete GL that we could use later on in the reports. Close and load it as connection only.

### Creating the Trial Balance

**Trial Balance** is basically a summary of all transactions as of a certain date. It shows the balance of our real and nominal accounts as of a certain date.

In other words, it shows the Balance Sheet and Profit & Loss in one statement. As such, we're going to create it next, and will be the basis of our P&L and Balance Sheet reports later on.

1. To start, create a blank query again in the Power Query windows. Right-click on the Queries Pane and choose **New Query > Other Sources > Blank Query**. This will create a template for our next query as follows.

    ![Blank Query](/img/creating-a-simple-accounting-system-with-excel-and-power-query/blank_query.png)
    <br>
    <br>

2. Open the Advanced Editor and change the above query to the following to get data from our **3_GL complete** query. Doing it this way, only the first two queries points to the tables in our worksheets. If we change the name of these tables, we only have to change them in the first two queries. 

    ![Source GL Query](/img/creating-a-simple-accounting-system-with-excel-and-power-query/source_gl_query.png)

    Note that I prefixed my queries in the order I created them for this blog. Note also the hashtag before the opening double quote as this is an **identifier (or variable)**, not just a string.
    <br>
    <br>

3. To get the trial balance from our GL, we have to group by Account Name/Account #, and aggregate the Amount column. Select the Account column, go to **Transform tab > Group By**. 

    ![Group GL](/img/creating-a-simple-accounting-system-with-excel-and-power-query/group_gl.png)

4. Expand the **AllRows** column to include only the Account Type. Remove resulting duplicates in the Account column. Then sort by Account #.

5. Add conditional columns for the **Debit** and **Credit** columns. The logic are as follows

	* Debit - If the Balance is positive, return the Balance. Else, return 0.
	* Credit - If the Balance is negative, return the Balance. Else, return 0.

    Adding the columns looks like this:

	![Debit Column Sample](/img/creating-a-simple-accounting-system-with-excel-and-power-query/debit_col.png)
    <br>
    <br>

6. For reporting purposes, negatives do not look good in our Credit column. Transform it by applying Absolute value function. Go to **Transform tab > Scientific > Absolute Value**. The query now looks like this.

	![Absolute Value Applied](/img/creating-a-simple-accounting-system-with-excel-and-power-query/absolute_val_applied.png)
    <br>
    <br>

7. Rearrange the columns as necessary. Our query now looks like this.
	
	![Final TB](/img/creating-a-simple-accounting-system-with-excel-and-power-query/final_tb.png)
    <br>

    Rename this query as **4_TB**. Alright, so we're now done with the Trial Balance. Next one is the Profit & Loss Statement.

### Profit & Loss Statement
The **Profit & Loss Statement** shows the financial performance of a company as of certain period. If you've done the Trial Balance in the previous section, this report will be a piece of cake.

1. Create a blank query again and get the data from the **4_TB** query. 
2. Filter the Account Type to include only the Revenue and Expenses account types.
	
	![Filter P&L](/img/creating-a-simple-accounting-system-with-excel-and-power-query/filter_pl.png)
    <br>
    <br>

3. Reverse the signs of the Balance column for presentation. We reverse the signs to show the Revenues as positive and Expenses as negative. You could do this by *multiplying Balance by -1* in a new column.
    <br>

4. Remove Account Type, Debit, Credit and Balance columns. Our query now looks like this.

	![Final P&L](/img/creating-a-simple-accounting-system-with-excel-and-power-query/final_pl.png)
    <br>
    <br>

    We're done with our Profit & Loss statement. The business has a Net Loss. Rename this query as **5_P&L**.

### Conclusion
In this tutorial, we have completed the Trial Balance and Profit & Loss statement for our accounting system. In the next post, we're going to do the Balance Sheet report.

Till then, **Stay Querious & Happy coding!**


