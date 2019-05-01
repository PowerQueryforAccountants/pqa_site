---
title: "Audit Procedures in Power Query: Duplicate Detection"
linktitle: "Audit Procedures in Power Query: Duplicate Detection"
date: 2019-04-28
draft: true
tags: [
    "groupby",
    "index.column",
    "List.First"
]
archives: ["2019"]
prev: /post/audit-procedures-in-power-query-gap-detection/
next: /post/audit-procedures-in-power-query-fraud-detection/
---

## Audit Procedures: Duplicate Detection with Power Query
<br>

This is the **Part 2** of the series on the **Audit Procedures in Power Query**.

If you missed the **Part 1**, you can read it **[here](/post/audit-procedures-in-power-query-gap-detection/)**

Duplicate Detection is a technique wherein duplicates are detected from a list of numbers. For example, determining the duplicate invoice numbers within a list of invoice numbers.

Having duplicates in the books is problematic for a lot of reasons. For example, if there are duplicates in the Invoice Numbers, this results to higher sales, higher taxes and therefore higher cash operating requirements. It may also indicate fraud as some employees may artificially beefing up the sales to increase commissions and bonuses.

Duplicate Detection is a helpful in order to test where controls are adequate and plan the appropriate substantive test to get reasonable assurance that financial statements are fairly stated.

Normally, we do this kind of test using specialized audit softwares which may cost hundred of dollars. Now, with the use of Power Query, we can now build these tools using Power Query easily at low cost.

### The Process
To follow along, you could download the exercise files from **[here](https://github.com/PowerQueryforAccountants/Duplicate-Detection-with-Power-Query)**.

1. Create a new workbook and named it **Duplicate Detection.xlsx**. Create a new query from Workbook and choose **Invoice List.xlsx**.
    The file **Invoice List.xlsx** contains the client-given invoices.

2. In the query, promote the first row as headers and change the column types as necessary. 

	The data should now look like this

	![Client Invoices](/img/audit-procedures-in-power-query-duplicate-detection/client_invoices.png)
    <br/>
    <br/>

3. Next, select the **Invoice Numbers** column and go to **Transform > Group By**

	![Group By](/img/audit-procedures-in-power-query-duplicate-detection/group_by.png)
	
    We are basically using the equivalent of **COUNTIF()** in the above step. The advantage of this is that we can easily reused this file and point to other files.
    This is also suitable for very larger files containing longer list of invoices. This is true for business that might be engage in e-commerge.

	The data should now look like this. Expand the **Details** table column but include only the **Amount**.

	![Expand Details](/img/audit-procedures-in-power-query-duplicate-detection/expand_details.png)
    <br/>
    <br/>

	The data should now look like this

	![Expanded Details](/img/audit-procedures-in-power-query-duplicate-detection/expanded.png)
    <br/>
    <br/>

4. Determining the duplicates now consists of filtering the rows where **Count > 1**. Doing that gives us the following
	
	![Duplicates](/img/audit-procedures-in-power-query-duplicate-detection/duplicates.png)
    <br/>
    <br/>

    The above table shows the invoice number and its duplicate. 

	Why did I chose to output the original invoice and it's duplicate? The reason is that we may have a duplicate that has a different amount. As part of the audit, we have to investigate why the amounts are different and ultimately deterimine which one is the correct amount.

    You could now load this to spreadsheet to do further analysis.


### Conclusion
In this tutorial, you have learned how to use Power Query to detect duplicate invoice numbers. This is another great addition to your toolbox to be used as part of the audit or to be incorporated in monthly closing processes.

If there are changes that may arise, like change in column names, it could easily be updated in the Power Query editor. Power Query is definitely a must-have in an auditor's toolbox.

You could reuse this workbook for future instances involving duplicate detection. Point the workbook to a new file, refresh the query, and you're good to go. 

That's it for now. In the next post, we're going to discuss how to use Power Query in order to detect fraud from our books.

**Stay Querious. Happy coding!**
