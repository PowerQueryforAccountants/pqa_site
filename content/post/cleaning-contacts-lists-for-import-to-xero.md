---
title: "Cleaning Contacts List for Import to Xero"
linktitle: "Cleaning Contacts List for Import to Xero"
date: 2019-04-06
tags: [
    "Xero",
    "data.transformation",
    "automation",
    "importing.data"
]

archives: ["2019"]
prev: /post/summarizing-time-logs-for-payroll/
next: /post/automating-transaction-import-from-sales-journal/
---

### Cleaning Contacts List for Import to Xero
<br>

There are times when you want to bulk import Contacts to Xero from an external system like a CRM. The problem is that CRM reports usually don't comply with the import format required by Xero.

In this tutorial, we're going to use Power Query in order to simplify the process of transforming our Contacts lists in a format that is suitable for format. You could download the file here and follow along.

### Let's review our data
To follow along, you could download the files **[here](https://github.com/PowerQueryforAccountants/Cleaning-Contacts-List-for-Import-to-Xero)**

Our data is basically formatted like this:

![Original Data](/img/cleaning-contacts-list-for-import/orig_data.png)

You would notice too that the **Address** is split into several lines. Moreover, the **Phone**, **Mobile**, and **Email** are labeled within the data itself. We have to separate these labels and make them as column headings.

### Preparing the original data
Basically, we have to divide our data into three parts that we're going to "stitch" later on using the **Code** column to arrive at the final transformed dataset.

1. In a blank workbook, create a query and choose the Contacts.xlsx file. Remove the first blank row and promote the second row as headers.
2. Copy this dataset 3 times. In effect, we're going to have 4 queries. We're going to change these 3 copies and the original one will retain as our back up.

### Getting the Company Name and Contact Person
As of this point, our data should look like this

![Initial Data](/img/cleaning-contacts-list-for-import/initia_data.png)

The first line of each data contains the *Company Full Name* and the *Contact Person*. In the first query copy, filter to include rows where **Code** is not null. Name this query **Full Name**

The data should now look look this.

![Full Name](/img/cleaning-contacts-list-for-import/full_name.png)

Leave this query for now and let's go to the a second copy of the original query.

### Getting the Phone, Mobile and Email field

In a second copy of the original query:

1. Duplicate the **Code** column. Right-click on original **Code** column and choose **Fill > Down**.
    
    ![Code Fill Down](/img/cleaning-contacts-list-for-import/code_fill_down.png)

2. Filter on the **Code-Copy** column to include rows containining null values. Filter on the **Contact Details** column to exclude null values. Remove the **Code-Copy** column. The **Contact Details** column now only include the rows contact the Phone, Mobile, and Email details.

    ![Contacts Only](/img/cleaning-contacts-list-for-import/contacts_only.png)
    <br/>
    <br/>

3. Let's split **Contact Details** column. Go to **Home > Split Column > By Delimiter**. Split on the **colon**

    ![Split Column](/img/cleaning-contacts-list-for-import/split_column.png)
    <br/>
    <br/>

    The reason for splitting is that we're going to pivot the table in the next step so that the **Phone**, **Mobile**, and **Email** labels will be column names and the values will be the rows. Delete the **Contact Details** column

    The data should now look like this

    ![Splitted Column](/img/cleaning-contacts-list-for-import/splitted_col.PNG)
    <br/>
    <br/>

4. Finally, pivot the data. Select the column containing the contact labels and go to **Transform > Pivot Column**

    ![Pivot Column](/img/cleaning-contacts-list-for-import/pivot_column.png)
    <br/>
    <br/>

    The data should now look like this

    ![Pivoted Column](/img/cleaning-contacts-list-for-import/pivoted_col.png)
    <br/>
    <br/>

    Rename this query to **Contacts**. Leave this for now and let's proceed with the third query.

### Combining Address in One Line
In the third query

1. Duplicate the **Code** column. Right-click on original **Code** column and choose **Fill > Down**.
    
    ![Code Fill Down](/img/cleaning-contacts-list-for-import/code_fill_down.png)
    <br/>
    <br/>

2. Filter on the **Code-Copy** column to include rows containining null values. Remove the **Contact Details** and Code-Copy columns. Filter the **Company Name && Address** column to exclude null values. We're now down to the rows containing the addresses.

    ![Address Only](/img/cleaning-contacts-list-for-import/address_only.png)
    <br/>
    <br/>
    
3. Now we have to group by **Code** in order to combine the addresses. Go to **Transform > Group By**
    
    ![Group Address](/img/cleaning-contacts-list-for-import/group_addr.png)
    <br/>
    <br/>

    Note that the Sum operation will not give us the address but we're going to use this as scaffold to our **code**. Open the query in **Advanced Editor** and change the following line 

    ```
    Table.Group(#"Filtered Rows1", {"Code"}, {{"Address", each List.Sum([#"Company Name && Address"]), type text}})
    ```

    To this
    ```
    Table.Group(#"Filtered Rows1", {"Code"}, {{"Addr", each List.Accumulate([#"Company Name && Address"],"", (state,current)=>state&", "&current)
    ```

    What this does is to simply concatenate the addresses separated by the **", "** character. The data should now look like this

    ![Grouped Address](/img/cleaning-contacts-list-for-import/grouped_addr1.png)
    <br/>
    <br/>

    There's a extra comma at the start of the **Address**. You could remove this by splitting the column at the left-most comma. Remove extra columns as appropriate

    ![Split Column](/img/cleaning-contacts-list-for-import/split_column1.png)
    <br/>
    <br/>

    After removing the extra comma, it should look like this

    ![Splitted Column](/img/cleaning-contacts-list-for-import/splitted_col1.PNG)

    Rename this query to **Address**. Leave this for now and let's proceed with merging our queries.

### Stitching the Data Together
Unfortunately, Power Query cannot merge multiple queries together. Since we have 3 queries, we have to create two merges to combine them.

You could combine the queries in any order but for this tutorial, I'm going to combine the Full Name and Address queries and afterwards merge the Contacts query. 

Let's do this.

1. Merge the queries **Full Name** and **Address** using a left-outer join. We use a left-outer join because the **Full Name** query has all the record we need.

    You could view the number of records for the other queries by selecting them and looking at the bottom of the Queries Pane like this

    ![Full Name Records](/img/cleaning-contacts-list-for-import/full_name_rec.PNG)
    <br/>
    <br/>

    Expand the table column to include the Address column only.

    ![Merge Full Name Address](/img/cleaning-contacts-list-for-import/merge1.png)
    <br/>
    <br/>

2. Merge the above Merge1 query with the Contacts query using a left-outer join again. Expand the table column to include only **Phone**, **Mobile**, **Email**, **Web**, and **Fax** columns.
    
    Now our data should look like this. 

    ![Final Merge](/img/cleaning-contacts-list-for-import/merge2.png)
    <br/>
    <br/>

    Load this to Excel and save as a CSV file as appropriate to be ready for import

### Conclusion
In this tutorial, you have learned how to use Power Query's in order to prepare a transformed data into an appropriate format suitable for import to other softwares. You have learned how to approach a difficult data transformation tasks by subdividing it to small problems.

You could reuse this workbook for future instances involving import of data to accounting software. Point the workbook to a new file, refresh the query, and you're good to go.

That's it for now. Check out other post here to learn more about Power Query.

**Stay Querious. Happy coding!**