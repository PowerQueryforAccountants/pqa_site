---
title: "Extracting a Trial Balance from a Ledger - Part 3"
linktitle: "Extracting a Trial Balance from a Ledger - Part 3"
date: 2019-01-19
draft: false
tags: [
    "groupby",
    "ledger",
    "trial balance"
]
archives: ["2019"]
prev: /post/extracting_trial_balance_from_ledger_p2/
---

### Extracting a Trial Balance from a Ledger - Part 3
<br>
This is the **Part 3** and last installment of a series on Extracting a Trial Balance from an Accounting Ledger using Power Query. You could view the previous tutorials on the following links

* [Part 1](/post/extracting_trial_balance_from_ledger_p1/)
* [Part 2](/post/extracting_trial_balance_from_ledger_p2/)

With that said, let's get started.

#### Filling Missing Data
At this point, our Account Column contains the combined Account Number and Name but only for each beginning balance. We have to populate the Account column with the last known value in it.

Power Query has the Fill Down function for this. However, before we could use it, we have to make sure that empty strings are replaced with nulls. Empty strings and nulls are actually different in Power Query. Null is the absence of any value, even empty string.

The steps to convert empty string to nulls are:

1. Remove spaces matching entire cells in the Account Column

Right-click on the Account column, and go to Transform > Format > Trim. Afterwards, right-click again, go to Transform > Format > Clean.
These two functions should remove trailing spaces and extra spaces from our Account column. Of course, if the cell contains only spaces, the result will be empty strings!

2. Replace empty strings with nulls.

![Replace empty strings with nulls](/img/extracting_trial_balance_from_ledger/replace_with_nulls.png)

Make sure you tick the `Match entire cell contents` option.

We could now, use Fill Down. While Account is still selected, right-click and choose Fill > Down.


![Fill down account](/img/extracting_trial_balance_from_ledger/fill_down_account.png)

We also need to put a date for all rows in our table containing beginning balances. We could do this by applying the opposite of Fill Down - Fill Up. What it does is to copy the last known value from below to a cell above.

Let's do this in our data. Select the `Date` column, right-click, go to Fill > Up.

![Fill up date](/img/extracting_trial_balance_from_ledger/fill_up_date.png)

Finally, we could remove other null values from our `Balance` column by filtering them out.

Our data should now look like this.

![Fill missing](/img/extracting_trial_balance_from_ledger/fill_missing.png)


#### Grouping our Data & Extracting the Last Item
We're almost done with our project. Our table is now in the format that we want and now we just have to get the last item from each group of accounts. 

We could do this in two steps.

1. Grouping our table by the `Account` column
2. Getting the last item for each group

To group our data, go to Transform > Group By. In dialog box, we're going to use the Sum operation to build our code syntax that we're going to edit in a moment. The Sum operation itself will not give us the Trial Balance we want.

![Group data](/img/extracting_trial_balance_from_ledger/group_data.png)

Now, we have the code that we need to edit in order to edit. In the formula bar, the current code for the end state of our data is as follows:

![List Sum](/img/extracting_trial_balance_from_ledger/list_sum.png)

Because of this, the `Ending Balance` column actually shows the sum of the `Balance` column for each account.

Go to the formula bar, and change it to the following. Essentially, we're changing `List.Sum` to `List.Last`

![List Last](/img/extracting_trial_balance_from_ledger/list_last.png)

Upon changing, you get the last value in the `Balance` column for each account. This is essentially now your **Trial Balance**.

You could now load this to your workbook. Hooray!

#### Bonus: Getting the Comparative Trial Balance
You might want to get a comparative trial balance by getting the opening balances of each account. 

You could do this by changing `List.Last` to `List.First` and it will fetch the beginning balances instead.
I recommend that you duplicate your query in order to get the opening balance and ending balance trial balances separately.

#### Conclusion
Hooray! We now have the Trial Balance at our disposal. In this series, we have learn how to use Power Query to extract the trial balance from a general ledger. We can even automate the process of getting the trial balance from our workbooks by "recycling" the same code that we used.

Power Query is really powerful and suitable especially for large files (like the one we used) instead of using cumbersome formulas that our makes your workbook heavy and prone to crashing. It is a valuable addition to an accountant's toolkit.

**Stay Querious. Happy coding!**