---
title: "Audit Procedures in Power Query: Gap Detection"
linktitle: "Audit Procedures in Power Query: Gap Detection"
date: 2019-04-27
draft: true
tags: [
    "groupby",
    "index.column",
    "List.First"
]
archives: ["2019"]
prev: /post/automating-transaction-import-from-sales-journal/
next: /post/audit-procedures-in-power-query-duplicate-detection/
---


## Audit Procedures: Gap Detection with Power Query
<br>

In this series, we're going to explore the use of Power Query in Auditing. Let's start with Gap Detection. 

**Gap Detection** is a technique wherein missing number are determined within a longer list of number. For example, determining the missing invoices within a list of invoice numbers.

The presence of **"gap"** may indicate understatement of sales and therefore lowering income taxes. It may also indicate that controls are not being followed as invoice numbers are not being used consecutively.

Gap Detection is a helpful in order to test where controls are adequate and plan the appropriate substantive test to get reasonable assurance that financial statements are fairly stated.

Normally, we do this kind of test using specialized audit softwares which may cost hundred of dollars. Now, with the use of Power Query, we can now build these tools using Power Query and save tons of money.

### The Process
Determining gaps within a list of invoices basically involve comparing two sets of data, the client-given invoices and the population of all invoices. This can be visualized with the help of the following

![Venn Diagram](/img/audit-procedures-in-power-query-gap-detection/gap_detection.png)

Client-given invoices (green circle) is a subset of all the invoices (red circle). Anything that's outside the green circle but within the red circle can be thought of as the missing invoices or the "gap".

In database parlance, this "gap" is the result of the left-anti join between the population of all invoices and and the client invoices.

Let's now proceed doing this in Power Query.

### Preparing the Population
To follow along, you could download the exercise files from **[here](https://github.com/PowerQueryforAccountants/Gap-Detection-with-Power-Query).**

1. Create a new workbook and named it **Gap Detection.xlsx**. Create a new query from Workbook and choose **Invoice List.xlsx**.
	The file **Invoice List.xlsx** contains the client-given invoices.

2. In the query, promote the first row as headers and change the column types as necessary. 

	The data should now look like this
	
	![Client Invoices](/img/audit-procedures-in-power-query-gap-detection/client_invoices.png)
	<br/>
	<br/>

3. Duplicate this query. From this query, we're going to **get the first and last invoice numbers and recreate the complete list of invoices**. This then will be the population of all invoices.
	
4. The invoice number is formatted as "INV" followed by the three-digit invoice numbers. To get the first and last invoice numbers, we have to get the numeric part of the Invoice Numbers first. Select the Invoice Numbers column and go to **Add Column tab > Extract > Last Characters** and put **3**.

	![Insert](/img/audit-procedures-in-power-query-gap-detection/insert.png)
	<br/>
	<br/>

	Convert the resulting column to numbers to extract the minimum and maximum value. The data should now look like this

	![Inserted](/img/audit-procedures-in-power-query-gap-detection/inserted.png)
	<br/>
	<br/>

5. Extract the minimum and maximum invoice numbers. Go to **Home tab > Advanced Editor** and add the following code
	
	```
	Min = List.Min(#"Changed Type1"[Last Characters]),
    Max = List.Max(#"Changed Type1"[Last Characters]),
    Custom3 = {Min..Max},
	```
	
	The above code extracts the minimum and maximum value from the invoice numbers (1st and 2nd line respectively). Then creates a list of numbers from the minimum up to the maximum invoice numbers (3rd line).

	The data should now look like this

	![Custom](/img/audit-procedures-in-power-query-gap-detection/custom.png)
	<br/>
	<br/>
	
	There's now a new **Transform** tab that appears at the top. This is a contextual tab as the resulting data from the step above is a list. 

	![New Transform](/img/audit-procedures-in-power-query-gap-detection/new_transform.png)
	<br/>
	<br/>

	Go to this tab and click on the **To Table**. Accept the defaults in the dialog box

	![Accept Defaults](/img/audit-procedures-in-power-query-gap-detection/accept_defaults.png)
	<br/>
	<br/>

6. Now we have to create a new column to combine the string **"INV"** with the invoice numbers. But before that, we have to convert our to invoice numbers to text. Concatenation only works for text data. 
	
	Then, prefix the invoice numbers too with zeroes. *The challenge here is that the number of zeroes to add depend on the invoice number length*. For example, if the number is "1", prepend with "00". If the number is "27", prepend with "0". Be sure the to convert to text format first as we can't prefix them with 0 if they are in numeric format.

	*The easiest way to do this is to add 2 zeroes and extract the last 3 characters from the resulting string*. From the example above, "27" will be "0027" then "027" after the said steps.

	Go to **Transform > Format > Add Prefix** and add **2** zeroes

	![Prefix](/img/audit-procedures-in-power-query-gap-detection/prefix.png)
	<br/>
	<br/>

	Now go to **Transform > Extract > Last Characters** and put **3**
	
	![Extract](/img/audit-procedures-in-power-query-gap-detection/extract.png)
	<br/>
	<br/>

	The data should like this after convertion

	![Prefixed](/img/audit-procedures-in-power-query-gap-detection/prefixed.png)
	<br/>
	<br/>

7. Create a new column to combine the **"INV"** to this column. Go to **Add Column > Custom Column**

	![New Column](/img/audit-procedures-in-power-query-gap-detection/new_col.png)
	<br/>
	<br/>

	Remove the original column. This now our list of **all the invoices (population)**

	![Population](/img/audit-procedures-in-power-query-gap-detection/population.png)
	<br/>
	<br/>

### Extracting the Missing Invoice Numbers
Now to extract the missing invoice numbers, we're going to do a **left-anti join of the population and the client-invoices**.

**Create a new merge query. Choose the **population query first and the client-invoices query second**. Choose **Left-Anti join**.
	
![Left-Anti](/img/audit-procedures-in-power-query-gap-detection/left-anti.png)

*These are now your missing invoice numbers*. If you expand the table column, this should contain nulls. The reason is that the resulting invoices numbers from the merge are the ones missing from the client-invoices hence they are all nulls. You could try this on your own!

Remove unnecessary columns. The final data should like this

![Final](/img/audit-procedures-in-power-query-gap-detection/final.png)

### Conclusion
In this tutorial, you have learned how to use Power Query to detect missing invoice numbers. With the proper technique, it could even replace standard audit packages that are expensive and outdated.

Also, if there are changes that may arise, like change in column names, it could easily be done in the Power Query editor. Power Query is definitely a must-have in an auditor's toolbox.

You could reuse this workbook for future instances involving gap detection. Point the workbook to a new file, refresh the query, and you're good to go. 

That's it for now. In the next post, we're going to discuss how to use Power Query in order to detect duplicate invoice numbers.

**Stay Querious. Happy coding!**


	





	







