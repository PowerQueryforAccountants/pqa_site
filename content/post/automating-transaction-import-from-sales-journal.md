---
title: "Automating Transaction Import from Sales Journal"
linktitle: "Automating Transaction Import from Sales Journal"
date: 2019-04-20
tags: [
    "groupby",
    "index.column",
    "List.First"
]
archives: ["2019"]
---

## Automating Transaction Import from Sales Journal
<br>

It is common that sales are recorded in a tracking sheet instead of directly adding them in an accounting software. Perhaps, a business has branches that maintains its own sales journal to be collected at the end of the day for recording; or it might be that recording of sales is handled by a clerk and as part of monthly processes these are imported to an accounting system.

In any case, Power Query can help on this one. 

### The Import File
To follow along, you could download the exercise files **[here](https://github.com/PowerQueryforAccountants/Automating-Transaction-Import-from-Sales-Journal)**.

In this example, we're going to import to a company's accounting software which is **[QuickBooks Online](https://quickbooks.intuit.com/online/)** using a third-party app called **[SaasAnt](https://saasant.com/)**. 

**[SaasAnt](https://saasant.com/)** provides a sample import template file to be used. From the exercise files, open the file named **SaasAnt_Excel_Transactions_Global_Template-Simple.xlsx** and explore it. Go to the sheet named **Invoices**.

![Import Template](/img/automating-transaction-import-from-sales-journal/template.png)
<br/>
<br/>

As you could see, the image contains a sample import format if you're to import invoices with multiple items. The relationship between the **QBO invoice** and the **template** can be visualized as follows

![Relationship Between Invoice and Import File](/img/automating-transaction-import-from-sales-journal/relationship.png)
<br/>
<br/>

The items are in separate lines while the other invoices details like invoice numbers, reference, etc. appeared just once for each invoices. Think of it this way

* **Invoice Headers** - includes details such as invoice numbers, customer name, etc. Appears at the first line for each invoice in the import file
* **Invoice Body** - includes details for the **Items**. Appears in individual lines for each invoice in the import file

Converting our sales data to the same format in the template can be tedious. Fortunately, we can do this in Power Query easily.

### The Process
The trick to do this is to use an **Index** column together with **GroupBy**. Let's do this.

1. Create a new workbook named **Sales Import.xlsx**. Create a new query from workbook and choose **Sales Journal.xlsx**
2. Remove unnecessary columns as appropriate.
3. Now, we have to unpivot the columns containing the products that we sell. Select all the columns with product names and go to **Transform > Unpivot columns**.

    The data should now look like this

    ![Pivoted Columns](/img/automating-transaction-import-from-sales-journal/pivoted.png)
    <br/>
    <br/>

    As you would notice, the product names are now in the column **Attribute** and the sales price in the **Value** column. The **Amount** column is the total invoice amount. **OR # and other details** are also now copied across the products belonging to the same invoice.

4. Now add an index column. Go to **Add Column tab > Index Column > From 1**.

    The data should now look like this

    ![Index Column](/img/automating-transaction-import-from-sales-journal/indexed.png)
    <br/>
    <br/>

5. Duplicate this query. On this duplicate, go to **Transform tab > Group By**. 

    ![Group By](/img/automating-transaction-import-from-sales-journal/groupby.png)
    <br/>
    <br/>

    Take note that the Sum function will not give us what we want but we're going to use it as scaffold to our code. 

    Now go to the **Advanced Editor** and change the following line

    ```
    #"Grouped Rows" = Table.Group(#"Added Index", {"OR #"}, {{"First", each List.Sum([Index]), type number}}),
    ```

    To this

    ```
    #"Grouped Rows" = Table.Group(#"Added Index", {"OR #"}, {{"First", each List.First([Index]), type number}}),
    ```

    Essentially, we want to get the first number in the **Index** column for each invoice and put it in the **First** column.

    The data should now look like this

    ![List First](/img/automating-transaction-import-from-sales-journal/grouped.png)
    <br/>
    <br/>

6. Let's create a merge query on the original and duplicate query. Match on the **Index** and **First** column and choose the **Left Outer** join.

    ![Merge](/img/automating-transaction-import-from-sales-journal/merge.png)
    <br/>
    <br/>

    Expand the table column and include only the **First** column. Sort the data by the **Index** column.

    The idea behind the sorting is to make sure that all items belonging to the same invoice are grouped together. 

    The data should now look like this

    ![Merged](/img/automating-transaction-import-from-sales-journal/merged.png)
    <br/>
    <br/>

7. The **First** column will serve as a clue to which row should we retain the **Invoice** header information while replacing the other rows with nulls. 
    
    Let's start with the **OR #**. Add a conditonal column for the **OR #**

    ![Conditional Columns](/img/automating-transaction-import-from-sales-journal/conditional_col.png)
    <br/>
    <br/>

    The data should now look this

    Add conditional columns for **Date of Sales**, **Amount**, and **Payment** method columns with the same logic above.
    Take note that the column names can't be the same with the existing ones hence you have to think of new column names.

    The data should look like this upon doing so

    ![New Conditional Columns](/img/automating-transaction-import-from-sales-journal/conditional_col1.png)
    <br/>
    <br/>

    Delete the **Date of Sales**, **Amount**, and **Payment** method columns. Rearrange columns as appropriate. 

8. Add another column to indicate the **Tax** for each item. Let's assume that it is **TaxInclusive** as it is the common business practice.

    ![Tax Column](/img/automating-transaction-import-from-sales-journal/tax_col.png)
    <br/>
    <br/>

    The data should now look this and ready for import.

    ![For Import](/img/automating-transaction-import-from-sales-journal/for_import.png)
    <br/>
    <br/>

### Conclusion
In this tutorial, you have learned how to use Power Query's in order to prepare a transformed data into an appropriate format suitable for import to other softwares. You have learned how to approach a difficult data transformation tasks by subdividing it to small problems.

You could reuse this workbook for future instances involving import of data to accounting software. Point the workbook to a new file, refresh the query, and you're good to go.

For complete instructions on how to do the import in **SaasAnt**, check out the article **[here](https://support.saasant.com/support/solutions/articles/14000053040-how-to-import-transactions-in-a-file-using-excel-transactions-app-)**.

That's it for now. Check out other post here to learn more about Power Query.

**Stay Querious. Happy coding!**






    







    