---
title: "Audit Procedures in Power Query: Fraud Detection"
linktitle: "Audit Procedures in Power Query: Fraud Detection"
date: 2019-04-28
draft: true
tags: [
    "fraud.detection",
    "benford's.law",
    "audit",
    "automation"
]
archives: ["2019"]
prev: /post/audit-procedures-in-power-query-fraud-detection/
next:
---

## Audit Procedures in Power Query: Fraud Detection
<br>

This is the **Part 3** and last installment of the series **Audit Procedures in Power Query**.

You could view the previous tutorials on the following links

**[Audit Procedures in Power Query: Gap Detection](/post/audit-procedures-in-power-query-gap-detection/)**
<br>
**[Audit Procedures in Power Query: Duplicate Detection](/post/audit-procedures-in-power-query-duplicate-detection/)**

In this tutorial, we're going to explore how to implement Benford's Law in Excel.

**Benford’s Law**, also known as the **Law of First Digits**, is the phenomenon that the first digits (or numerals to be exact) of the numbers found in series of records of the most varied sources do not display a uniform distribution, but rather are arranged in such a way that the digit “1” is the most frequent, followed by “2”, “3”, and so in a successively decreasing manner down to “9”.

It is illustrated in the figure below.

![Benford's Law](/img/audit-procedures-in-power-query-fraud-detection/benford.png)
<br/>
<br/>

It has been shown that this result applies to a wide variety of data sets, including electricity bills, street addresses, stock prices, house prices, population numbers, death rates, lengths of rivers, etc.

With this property, it can be utilized to detect pattern (or lack thereof) in naturally occurring datasets. The most important application of this in audit is in **fraud detection**.

With that said, let's do this in Power Query.

### The Dataset
To follow along, you could download the exercise files **[here](https://github.com/PowerQueryforAccountants/Fraud-Detection-with-Power-Query)**.

The workbook **general_ledger.xlsx** contains general ledger data of a company for 20 years.

Benfords works best on data that:

* represents numeric values, such as populations of cities, lengths of rivers, or sizes of celestial bodies;
* do not have pre-established minimum or maximum limits.
* where numbers 1 to 9 have equal chances of appearing as first digits of measurements. For example, basketball player heights are not appropriate for Benford's Law as measurements tends to be somewhere in the 6 to 8 ft length. There are no measurements for 1 to 4 (or even 5) as they are do not usually qualify for the game.
* are not made up of numbers used as identifiers, such as identity or social security numbers, bank accounts, telephone numbers; and
* have a mean which is less than the median, and the data is not concentrated around the mean

Having said that, let's fire up Power Query and do this.

### The Process
With the exercise files downloaded.

1. In a new workbook, create a query from a Workbook and choose **general_ledger.xlsx**.
2. Promote headers and change the data type of columns as necessary. The data should look like this
    
    ![Initial](/img/audit-procedures-in-power-query-fraud-detection/initial.png)
    <br/>
    <br/>

3. Now, create a new column named **Transactions** and add the **Debit** & **Credit** columns. We're going to the Benford Analysis on this new column.
    
    ![Transaction Column](/img/audit-procedures-in-power-query-fraud-detection/transaction_col.png)
    <br/>
    <br/>
    
    Take note that we use `List.Sum()` instead of just adding the two columns as we have to consider the case where the value is null. 

    `Null` is a special data type in Power Query and is not the same with numbers. It is the absence of value, even an empty string.

    Other way to do this is to replace nulls in `Debit` & `Credit` columns with 0 and do the ordinary addition operation.

4. In the **Transactions** column, filter out the rows where **Transactions** = null

    ![Filter Nulls](/img/audit-procedures-in-power-query-fraud-detection/null_trans.png)
    <br/>
    <br/>

5. Now create the column that will contain the extracted first digits from the **Transactions** column. Select **Transactions** column and go to **Add Column > Extract > First Characters**.
    
    ![Add First Digits](/img/audit-procedures-in-power-query-fraud-detection/add_first_digits.png)
    <br/>
    <br/>
    
    Rename this column to **First Digits**.

6. Now group the rows by the **First Digits** to count its occurence

    ![Group Count](/img/audit-procedures-in-power-query-fraud-detection/group_count.png)
    <br/>
    <br/>

7. Filter the **First Digits** to exclude the 0. 

    ![Filter First Digit](/img/audit-procedures-in-power-query-fraud-detection/filter_first_digit.png)
    <br/>
    <br/>

    Remember that Benford's Law is a pattern for the occurence of the numbers 1 to 9 as first digits in datasets. The 0 in our dataset refers to transactions which are less that 1 (e.g. 0.87).

    We are not going to round these numbers as we don't want to arbitrarily change the occurence of digit 1 (e.g. 0.87 is rounded to 1, and so forth). Better just to removed these numbers.

8. Change the data type of **First Digits** column to decimals and sort in increasing order. As you could now, the count is also in increasing order

    ![Sort First Digit](/img/audit-procedures-in-power-query-fraud-detection/sort_first_digits.png)
    <br/>
    <br/>

9. Add another column to get the percentage of **Count** column over the total count. Go to **Add Column > Custom Column** and enter the following
    
    ![Percent First Digit](/img/audit-procedures-in-power-query-fraud-detection/percent_first_digit.png)
    <br/>
    <br/>

10. Now, create another column for the expected occurence of each digit 1 to 9. The formula is given by
    
    ![Benford Expected](/img/audit-procedures-in-power-query-fraud-detection/benford_expected.png)
    <br/>
    <br/>

11. Change the format of the **Percent** and **Benford's Expected** column to percentage. The final table should like this
    
    ![Final](/img/audit-procedures-in-power-query-fraud-detection/final.png)
    <br/>
    <br/>

    As you could see, the **Percent** and **Benford's Expected** column are approximately equal. We could **reasonably** conclude then that the dataset is free of fraud.

    Take note of the word **reasonably**. We **cannot absolutely conclude** that there's is or isn't a fraud using Benford's Law. However, we could do further analysis if the percentages are significantly different from the expected percentages of the Benford's Law.


### Conclusion
In this tutorial, you have learned how to use Power Query to implement the Benfor's Law. This is another great addition to your toolbox to be used as part of the audit. The value comes with the easy transformation of our data with few clicks to arrive at the analysis. Imagine doing this with Excel with the `LEFT()` and `COUNTIF()` formulas.

In addition, should there be any changes that may arise, like change in column names, it could easily be updated in the Power Query editor. Power Query is definitely a must-have in an auditor's toolbox.

You could reuse this workbook for future instances involving duplicate detection. Point the workbook to a new file, refresh the query, and you're good to go. 

That's it for now. Check out other post in this blog to learn more Power Query tips.

**Stay Querious. Happy coding!**
