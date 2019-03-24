---
title: "Extracting a Trial Balance from a Ledger - Part 2"
linktitle: "Extracting a Trial Balance from a Ledger - Part 2"
date: 2019-01-12
draft: false
tags: [
    "groupby",
    "ledger",
    "trial.balance"
]
archives: ["2019"]
prev: /post/extracting_trial_balance_from_ledger_p1/
next: /post/extracting_trial_balance_from_ledger_p3/

---

### Extracting a Trial Balance from a Ledger - Part 2

<br>
This is the Part 2 on Extracting a Trial Balance from an Accounting Ledger using Power Query.
If you missed the Part 1, you can read it [here](/post/extracting_trial_balance_from_ledger_p1/)

#### Removing Section Headers
At this point, all of the empty rows and empty columns from our general ledger have been remove. The next to do is remove the section headers.

Open up PQ editor by going to Data > Show Queries. Then click on our query (mine is named "Sheet1").

As you can see, our first column contains only the section headers. In our original Excel file, these are the merge cells. When they were imported, merging has removed and remain in the first column. To remove them, we simply filter the first column to include only null values.

![Remove Section Headers](/img/extracting_trial_balance_from_ledger/remove_section_headers.png)

#### Removing Report Metadata

The next we're going to do is to remove the report metadata. These are actually not the data itself, but rather data that describes our data. These includes the page number, the date when the data is printed, and so on. 

We could remove these by filtering them out from the 3rd column. Take note that we're not going to remove the GL ID as we're going to need them later on.

![Remove Report Metadata](/img/extracting_trial_balance_from_ledger/remove_report_metadata.png)

#### Removing Column Headers
Our data is beginning to take our desired format. However, it still has duplicate column headers. We have to promote the first row to column headers then remove the duplicates. 

To promot headers, go to Home > Use First Row as Headers

![First Row as Headers](/img/extracting_trial_balance_from_ledger/first_row_as_headers.png)

Then, we remove the duplicate column headers. Filter out the word "Date" from the second column (which is now named `Date` cause we've just promoted the first row as headers)

![Remove duplicate col headers](/img/extracting_trial_balance_from_ledger/remove_dupl_col_headers.png)

#### Adding Accounts Column
We now create a new column that will contain both the Account Numbers and Account Name. We're going to use this column later on but we're going to add this now to further clean up our data.

There are two ways to do this:
1. Add a custom column 
2. Merge the columns

Let's go with the merge option as it has the side effect of removing the old columns. Select the two columns to be combined and right-click Merge Columns. Name this new column as `Account`.

![Merge Col](/img/extracting_trial_balance_from_ledger/merged_column.png)

Move this new column at the start of our table by dragging the header at the start.

#### Removing Unnecessary Columns
Due to transformations we've made, some columns now contain null data, while some contains just labels. We need to remove these columns. In our table, select `Column1`, `Column3`, `Column5` & `Column8`. Note that these are the columns that don't have the headers that we promoted. Also select `Debit`, `Credit`, and `Balance` columns. These columns have headings but the values are in the adjacent columns. Right-click then `Remove Columns`

![Remove Columns](/img/extracting_trial_balance_from_ledger/remove_columns.png)


Let's rename also our columns to further tidy this up. Rename the last four columns as `Description`, `Debit`, `Credit`, `Balance`. Our data should now look like this:

![Part 2 End](/img/extracting_trial_balance_from_ledger/part2_end.png)


#### Conclusion
That's it for now. In the next and last part of this series, we're going to fill missing data from our table then extract the trial balance. Click on this link to view [Part 3](ledger_to_trial_balance_part_3.html) of this tutorial.

**Stay Querious. Happy coding!**
