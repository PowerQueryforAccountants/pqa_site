---
title: "Extracting a Trial Balance from a Ledger - Part 1"
linktitle: "Extracting a Trial Balance from a Ledger - Part 1"
date: 2019-01-05
tags: [
    "groupby",
    "ledger",
    "trial.balance"
]

archives: ["2019"]
next: /post/extracting-trial-balance-from-ledger-p2/
---

## Extracting a Trial Balance from a Ledger - Part 1
<br>
There are times when a client gives an Excel file containing all the transactions for all of its accounts and our task is to extract the trial balance from it.

We might do this to:

* Import the trial balance into another software. Perhaps transition to cloud accounting software from a legacy desktop software.
* Create the customized financial statements out of the trial balance.
* For audit or due diligence procedures.

In that case, Power Query could help up us do this.

### Let's review our data
For this article, we're going to use a sample ledger file in Excel. You can download the file **[gl_file.xlsx](https://github.com/PowerQueryforAccountants/Extracting-Trial-Balance-from-a-Ledger)** here. 

Some overview of the data that we have:

* It contains data from the April 1, 2017 to March 31, 2018 for a fictitious company Acme Corporation. 
* The transactions are arrange by date and grouped by Account. For example, in row 29 the balance is 67965.95 which can be derived by adding the 198.35 to the previous balance, 67767.60, in row 28.
* It also contains almost 40,000 rows of data. 
* There are blank rows between each transaction row (they have shorter height).
* Lastly, the file is almost 3MB in size.

Adding formulas in this workbook to clean the data might work but will be cumbersome due to the layout of the data. In addition, formulas will add to the file size making it prone to crashing.

Fortunately, we have Power Query in order to clean this file.

### What is required?
Basically, when cleaning up data like this, we should strive to convert our data into a table format. In other words,

* There should be no blank rows, and blank columns.
* There should be no unnecessary headers like section headers. There should be only one set of headers which serves as column names.

After converting it to a table, then that's the time that we can extract the trial balance from it.
Let's jump on to Power Query do our first pass of cleanup.

### Loading our data in Power Query
* Create a new Excel file in the same directory where you put the raw file. Let's name this **extracted_tb.xlsx**
* Open **extracted_tb.xlsx** and create a new query from Workbook. Choose **gl_file.xlsx**.

    ![New Excel File](/img/extracting-trial-balance-from-ledger-p1/new_query.png)
    <br/>
    <br/>
* Choose **Sheet1** and click on **Edit**.
    
    ![Edit Sheet 1](/img/extracting-trial-balance-from-ledger-p1/edit_sheet1.png)
    <br/>

### Removing the empty rows and columns
The easiest that we could do in any data cleanup is to remove blank rows and columns.

To remove empty rows, go to **Home > Remove Rows > Remove Blank Rows**.

![Remove Empty Rows](/img/extracting-trial-balance-from-ledger-p1/remove_empty_rows.png)
<br/>
<br/>

The next we could do is remove blank columns. Power Query does not have a built in option to remove empty or blank columns. This is kinda annoying and I don't know why this is the case.

Other Power Query users are transposing the data to turn columns into rows, so that they could apply Remove Blank Rows, then transpose the data back to its original layout. 

Personally, I don't like this workaround as it is not feasible for very large datasets (Power Query will crash). I would prefer to use a custom function to remove empty columns. Fortunately, we have just that function below

```
(tbl) =>
let
    Headers = Table.ColumnNames(tbl),

    Result = Table.SelectColumns(
                 tbl,
                 List.Select(Headers, each List.MatchesAny(Table.Column(tbl, _), each _ <> null)))
in
    Result
```
<br>

* To remove empty columns, right-click on the **Queries Pane** of the editor and choose **New Query > Other Query > Blank Query**.
    
    ![New Blank Query](/img/extracting-trial-balance-from-ledger-p1/blank_query.png)
    <br/>
    <br/>

* Copy the code above to this query using the **Advanced Editor** in the **Home** Tab

    ![Copy to Editor](/img/extracting-trial-balance-from-ledger-p1/copy_editor.png)
    <br/>
    <br/>

* Rename this query to **fnRemoveEmptyColumns**. The **fn** prefix is just my convention but this makes it clear that we are using a custom function when we put this in our Power Query code.
    
    Take note also that the **fx** symbol at the left side of the query in the Query Pane denotes that this query is a function.
    
    ![fx](/img/extracting-trial-balance-from-ledger-p1/fx_symbol.png)

* Go back to the original query. In the formula bar, click on the **fx** symbol to invoke a function.

    ![fx Button](/img/extracting-trial-balance-from-ledger-p1/fx_button.png)

    The formula will now be highlighted. 
    <br/>
    <br/>

* Type in `fnRemoveEmptyColumns(#"Removed Top Rows")` and press **Enter**

    ![Invoke Function](/img/extracting-trial-balance-from-ledger-p1/invoke_function.PNG)
    <br/>
    <br/>
    Empty columns are now removed.
    <br/>
    <br/>

* Close the PQ editor. When prompted to Load results, choose **Only Create Connection**
<br/>

### Conclusion
That's it for now. In the next tutorial, we're going to remove section headers and further remove blank cells until we're down to our general ledger table. Click on this link to view **[Part 2](../extracting-trial-balance-from-ledger-p2/)** of this tutorial.

**Stay Querious. Happy coding!**
