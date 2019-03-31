---
title: "Extracting a Trial Balance from a Ledger - Part 1"
linktitle: "Extracting a Trial Balance from a Ledger - Part 1"
date: 2019-01-05
draft: false
tags: [
    "groupby",
    "ledger",
    "trial.balance"
]

archives: ["2019"]
next: /post/extracting_trial_balance_from_ledger_p2/
---

### Extracting a Trial Balance from a Ledger - Part 1
<br>
There are times when a client gives an Excel file containing all the transactions for all of its accounts and our task is to extract the trial balance from it.

We might do this as means to following:

* Import the trial balance into another software. Perhaps transition to cloud accounting software from a legacy software
* Create the customized financial statements out of the trial balance.
* For audit or due diligence procedures.

In that case, Power Query could help up us do this.

#### Let's review our data
For this article, we're going to use a file provided by the client. Download the file [gl_file.xlsx](https://github.com/kennethjhim/Power-Query-for-Accountants/tree/master/Ledger%20to%20Trial%20Balance) here.

* It contains data from the April 1, 2017 to March 31, 2018 for a fictitious company Acme Corporation. 
* The file is divided into sections where each sections contain the transactions for a particular account. 
* The transactions are arrange by date. For example, in row 29 the balance is 67965.95 which can be derived by adding the 198.35 to the previous balance, 67767.60, in row 28.
* It also contains almost 40,000 rows of data. 
* There are blank rows between each value (they have shorter height).
* Lastly, the file is almost 3MB in size.

Adding formulas in this workbook to clean the data might work but will be cumbersome due to the layout of the data. In addition, formulas will add to the file size making it prone to crashing.

Fortunately, we have Power Query in order to clean this file.

#### What is required?
Basically, when cleaning up data like this, we should strive to convert our data into a table. In other words,

* There should be no blank rows, and blank columns
* There should be no unnecessary headers like section headers. There should be only one set of column headers.

After converting it to a table, then that's the time that we can extract the trial balance from it.
Let's jump on to Power Query do our first pass of cleanup.

#### Loading our data in Power Query
* Create a new Excel file in the same directory where you put the raw file. Let's name this `extracted_tb.xlsx`
    
    ![New Excel File](/img/extracting_trial_balance_from_ledger/new_excel_file.png)

* Open `extracted_tb.xlsx` and go to Data > New Query > From Workbook.
* In the dialog box that appears, choose `gl_file.xlsx`
* Choose Sheet1 from the Wizard and click on Edit.
    
    ![Edit Sheet 1](/img/extracting_trial_balance_from_ledger/edit_sheet1.png)

* The data now loads in the Power Query ("PQ") editor.

    ![Sheet Loaded](/img/extracting_trial_balance_from_ledger/sheet_loaded.png)

#### Removing the empty rows
The easiest that we could do in any data cleanup is to remove blank rows and columns. They are easy to implement and guaranteed to make your data much suitable for analysis.

We could remove empty rows by going to Home > Remove Rows > Remove Blank Rows.

![Remove Empty Rows](/img/extracting_trial_balance_from_ledger/remove_empty_rows.png)

#### Removing empty columns
The next we could do is remove blank columns. Power Query does not have a built in option to remove empty or blank columns. It has built-in for removing empty rows though. 

Other Power Query users are transposing the data so that columns would turn into rows, apply the Remove Blank Rows, then transpose the data to convert it back to the original layout. 

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

**To remove empty columns**

* In the Queries Pane of PQ editor, right-click on the queries editor and choose New Query > Other Query > Blank Query.
    
    ![New Blank Query](/img/extracting_trial_balance_from_ledger/blank_query.png)

* Copy the code above to this query using the Advanced Editor in the Home Tab

    ![Copy to Editor](/img/extracting_trial_balance_from_ledger/copy_editor.png)

* Rename this query to fnRemoveEmptyColumns. Take note that this query is denoted by the fx symbol
    
    ![fx](/img/extracting_trial_balance_from_ledger/fx_symbol.png)

  as this is a function.
    
* Go back to the original query. In the formula bar, click on the `fx` symbol

    ![fx Button](/img/extracting_trial_balance_from_ledger/fx_button.png)

  to invoke a function. 

* Type in `fnRemoveEmptyColumns(#"Removed Top Rows")` and Enter

    ![Invoke Function](/img/extracting_trial_balance_from_ledger/invoke_function.PNG)

  Empty columns are now removed.

* Close the PQ editor. When prompted to Load results, choose **Only Create Connection**

#### Conclusion
That's it for now. In the next tutorial, we're going to remove section headers and further remove blank cells until we're down to our general ledger table. Click on this link to view [Part 2](ledger_to_trial_balance_part_2.html) of this tutorial.

**Stay Querious. Happy coding!**
