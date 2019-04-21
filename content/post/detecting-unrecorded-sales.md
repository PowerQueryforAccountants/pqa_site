---
title: "Detecting Unrecorded Sales"
linktitle: "Detecting Unrecorded Sales"
date: 2019-03-09
tags: [
    "unrecorded.sales",
    "fictitious.sales",
    "merge",
    "join"
]
archives: ["2019"]
prev: /post/get-ending-inventory-under-fifo-p3/
next: /post/summarizing-time-logs-for-payroll/
---

### Detecting Unrecorded Sales
<br>

Power Query can also be used to detect any unrecorded sales. With its powerful grouping and merge functionality, we can compare large datasets with ease. 

Let's explore this.

### Review of our Data
We have two datasets to deal with. The first one is our Shopify data and the other one is the Xero invoices. Shopify is integrated to the client's Xero account and hence Shopify invoice are "push" to Xero. 

Due to some technicalities, this integration may fail and results to unrecorded sales. In this instances, we have to record the invoices manually in Xero.

To follow along, you could download the files **[here](https://github.com/PowerQueryforAccountants/Detecting-Unrecorded-Sales)**.

### Strategy: "VLOOKUP" on steroids
The strategy in finding these unrecorded sales is similar with the use of the good old VLOOKUP. We find a key in the Shopify Sales, match the corresponding key in Xero sales and return the corresponding sales. If we can't get a corresponding sales, it only means that the Shopify sale is not recorded.

This time we use Power Query's merge feature. Couple of advantage with using this:

* Automation - we can do this repeatedly with a different workbook without having to recreate VLOOKUP formulas. All we have to do is to point the query to the right files and refresh.
* Preprocessing - if you have data that needs to be preprocessed first before we can make comparisons, like trimming Invoice numbers or combining fields, Power Query would be a life-saver.
* Minimal file size - since we're not going to use formulas, file size would be smaller
* Suitable for large datasets - this is suitable for large datasets as we're not going to use formulas. Actual datasets are also kept intact.

### Preliminaries: Reviewing our data
Let's preview our Shopify Sales first.

Open up **Per Shopify.xlsx** and browse the data. Focus on the the column **Order**. As you could you see, this column contains invoice numbers for sales. The invoice numbers are not unique as each invoice item has its own lines. For example, INV2320 is broken down into 5 lines. Each line pertains to different items included in that invoice.

Now, let's go to our **Per Xero.xlsx** file. The column that we want to match with the **Order** column in **Per Shopify.xlsx** is the **Reference** column. The **Reference** column also contains duplicate as each invoice item has its own line. Moreover, it has blanks too. The blanks are valid entries to the system but they are not coming from Shopify. Perhaps they are store/offline sales. We have to remove these blanks too.

In order to successfully merge the two files, we have to make sure that the columns to be match (called the **'key'**) has unique values. To do that, we have to group our data by they key and arrive at a total invoice amount per line.

We could easily do that with Power Query.

### Preparing our data
Let's clean first **Per Shopify.xlsx**. In a new workbook named **Analysis.xlsx**:
1. Create a new query from Workbook and choose the **Per Shopify.xlsx file**

2. Remove unnecessary columns. We are only concern with **Order**, **Billing country**, **Amount (after discounts before tax)**, **Taxes**, and **Amount (after discounts and taxes)** columns.

3. Make sure that the **Order** column does not have any trailing spaces. These could throw off our Grouping later on so we have to make sure that values in this column are formatted consistently. Go to **Transform tab > Format > Trim** to trim trailing spaces and **Transform tab > Format > Clean** also to remove nonprinting characters.

    The data should now look like this

    ![Cleaned Data](/img/detecting-unrecorded-sales/cleaned_data.png)
    <br/>
    <br/>

4. Now, let's group by the **Order** column. Go to **Transform > Group by**. Group by **Order** column and sum the **Amount (after discounts before tax)**, **Taxes**, and **Amount (after discounts and taxes)** columns.

    ![Grouped By](/img/detecting-unrecorded-sales/grouped_by.png)
    <br/>
    <br/>

    The data should now look like this

    ![Grouped Data](/img/detecting-unrecorded-sales/grouped_data.png)
    <br/>
    <br/>
    


Now, let's go to our **Per Xero.xlsx** file.

1. In the same workbook, create another query and choose **Per Xero.xlsx**
2. We have to extract the Invoice number from the **Reference** column. Go to **Transform > Extract Last Characters**. Extract the last **7** characters.
    Take note that there's also **Extract Last Characters** in the **Add Column** tab but this will create another column for the extracted characters.

    The data should now look like this
    ![Extract Characters](/img/detecting-unrecorded-sales/extracted_chars.png)
    <br/>
    <br/>

3. Trim and clean this column.
4. Filter also the rows where **Reference** is blank or null.
5. Lastly, group our data by the **Reference** column. Sum the **Gross**, **GST**, and **Net Amount** columns.

    The data should now look like this

    ![Grouped Data2](/img/detecting-unrecorded-sales/grouped_data2.png)
    <br/>

### Merging Our Data
Now that our data we're already cleaned and group by Invoice Number, we can merge them now. To determine unrecorded sales we have to get the **Invoices in Shopify data that are not in the Xero data**. 

In database parlance, we have to get **Shopify left-anti join Xero**. Visualizing in a **Venn diagram**, **left-anti join** is the part where it says **"Shopify-Only Sales"**

![Left Anti Join](/img/detecting-unrecorded-sales/venn.png)

To get this in Power Query

1. In the **Analysis.xlsx** workbook, create a merge query.
2. Choose the **Shopify** as the first data, **Xero** as the second data. 
    Click on the column containing our Invoices in each of the tables. Basically, this will be the piece that will "stitch" the data together. Choose left-anti join.

    ![Shopify Only Merge](/img/detecting-unrecorded-sales/shopifyonlysalesmerge.PNG)
    <br/>

    The data should now look like this
    ![Shopify Only Data](/img/detecting-unrecorded-sales/shopifyonlysalesdata.PNG)
    <br/>

4. Let's rename this merge as **ShopifyOnlySales** as we want to get the sales which are in Shopify only (unrecorded in Xero). Expand the **Per Xero** column to include the **Gross** amount. Include the original table headings to distinguish it from the Shopify Sales.

    If you did it correctly, the gross amount from Xero should be **null**. These are the unrecorded sales.
    ![Unrecorded Sales](/img/detecting-unrecorded-sales/unrecorded_sales.png)
    
### Bonus: Detecting Fictitious Sales
We have determine unrecorded sales by using **left-anti join**. But what if we flip the order of the data that we joined. What if we choose Xero as our first data and the Shopify as the second one. What if we get **Xero-only sales**?

In that case, we could possibly uncover two things:

1. **Fictitious sales**
2. **Sales erroneously tag as Per Shopify**

Certainly, the first one would be our most concern. It has the effect of increasing sales taxes and commissions if there's any.

To get extract this, just follow the previous steps for the merge. But this time, choose *Xero first and Shopify data as second and choose left-anti join*.


### Conclusion
In this tutorial, you have learned how to use Power Query's merge feature in order to uncover either unrecorded sales or fictitious sales in your books. If this is a recurring concern in your monthly accounting process, Power Query can help automate this.

That's it for now. Check out other post here to learn more about Power Query.

**Stay Querious. Happy coding!**