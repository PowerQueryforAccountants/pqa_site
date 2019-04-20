---
title: "Extracting a Trial Balance from a Ledger - Part 3"
linktitle: "Extracting a Trial Balance from a Ledger - Part 3"
date: 2019-01-19
draft: false
tags: [
    "groupby",
    "ledger",
    "trial.balance"
]
archives: ["2019"]
prev: /post/extracting-trial-balance-from-ledger-p2/
---

## Extracting a Trial Balance from a Ledger - Part 3
<br>

This is the **Part 3** and last installment of the series **Extracting Trial Balance from a Ledger**.

You could view the previous tutorials on the following links

**[Extracting Trial Balance from a Ledger - Part 1](/post/extracting-trial-balance-from-ledger-p1/)**
<br>
**[Extracting Trial Balance from a Ledger - Part 2](/post/extracting-trial-balance-from-ledger-p2/)**

With that said, let's get started.

### Replacing Empty Strings with Nulls
At this point, our **Account** Column contains the combined Account Number and Name but only for each beginning balance. We have to populate the **Account** column with the last known value in it.

Power Query has the **Fill Down** function for this. However, before we could use it, we have to make sure that **empty strings are replaced with nulls**.

Empty strings and nulls are actually different in Power Query. **Null** is the absence of any value, even an empty string. It is denoted by an italized *null*.

The steps to **convert empty string to nulls** are:

1. Remove spaces matching entire cells in the Account Column

    Right-click on the Account column, and go to Transform > Format > Trim. Afterwards, right-click again, go to Transform > Format > Clean.
    These two functions should remove trailing spaces and extra spaces from our Account column. Of course, if the cell contains only spaces, the result will be empty strings!
    <br/>
    <br/>

2. Replace empty strings with nulls.

    ![Replace empty strings with nulls](/img/extracting-trial-balance-from-ledger-p3/replace_with_nulls.png)
    <br/>
    <br/>

    Make sure you tick the **Match entire cell contents** option.

At this point, all empty strings in the **Account** column are now replaced with nulls.

### Filling Missing Data
We could now, use **Fill Down**. While **Account** is still selected, **right-click** and choose **Fill > Down**.

![Fill down account](/img/extracting-trial-balance-from-ledger-p3/fill_down_account.png)
<br/>
<br/>

We also need to put a date for all rows in our table containing beginning balances. We could do this by applying the opposite of **Fill Down** - **Fill Up**. What it does is to copy the last known value from below to a cell above.

Let's do this in our data. Select the **Date** column, right-click, go to **Fill > Up**.

![Fill up date](/img/extracting-trial-balance-from-ledger-p3/fill_up_date.png)
<br/>
<br/>

Finally, we could remove other null values from our **Balance** column by filtering them out.

Our data should now look like this.

![Fill missing](/img/extracting-trial-balance-from-ledger-p3/fill_missing.PNG)

### Grouping our Data & Extracting the Last Item
We're almost done with our project. Our table is now in the format that we want and now we just have to get the last item from each group of accounts. 

We could do this in two steps.

1. Grouping our table by the **Account** column
2. Getting the last item for each group

To group our data, go to **Transform > Group By**. 

In dialog box, we're going to use the **Sum** operation to serve as our scaffold for our that we're going to edit in a moment.

![Group data](/img/extracting-trial-balance-from-ledger-p3/group_data.png)
<br/>
<br/>

Now, we have the code that we need to edit in order to edit. In the formula bar, the current code for the end state of our data is as follows:

![List Sum](/img/extracting-trial-balance-from-ledger-p3/list_sum.png)
<br/>
<br/>

Change it to the following. Essentially, we're changing `List.Sum` to `List.Last`

![List Last](/img/extracting-trial-balance-from-ledger-p3/list_last.png)
<br/>
<br/>

Upon changing, you get the last value in the **Balance** column for each account. This is now your **Trial Balance**.

You could now load this to your workbook.

**Hooray!!!**

### Bonus: Getting the Comparative Trial Balance
You might want to get a comparative trial balance by getting the opening balances of each account. 

You could do this by changing `List.Last` to `List.First` and it will fetch the beginning balances instead.
I recommend that you duplicate your query in order to get the opening balance and ending balance trial balances separately.

### Conclusion
We now have the Trial Balance at our disposal. 

In this series, we have learn how to use Power Query to extract the trial balance from a general ledger.

Power Query is really powerful and suitable especially for large files (like the one we used) instead of using cumbersome formulas that can make your workbook heavy and prone to crashing.

That's it. Check out other post here to learn more about Power Query.

**Stay Querious. Happy coding!**