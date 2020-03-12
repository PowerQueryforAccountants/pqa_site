---
draft: true
title: "Creating a Simple Accounting System with Excel and Power Query - Part 4"
linktitle: "Creating a Simple Accounting System with Excel and Power Query - Part 4"
date: 2019-10-19
tags: [
    "accounting.system",
    "data.validation",
    "named.range",
    "general.ledger",
    "trial.balance",
    "balance.sheet",
    "income.statement"
]
archives: ["2019"]
prev: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p3/
next: 
---

## Creating a Simple Accounting System with Excel & Power Query - Part 4
<br>

This is the **Part 4** of the series **Creating a Simple Accounting System with Excel & Power Query**.

If you missed previous tutorials, you could view them on the following links

**[Creating a Simple Accounting System with Excel & Power Query - Part 1](../creating-a-simple-accounting-system-with-excel-and-power-query-p1/)**
<br>
**[Creating a Simple Accounting System with Excel & Power Query - Part 2](../creating-a-simple-accounting-system-with-excel-and-power-query-p2/)**
<br>
**[Creating a Simple Accounting System with Excel & Power Query - Part 3](../creating-a-simple-accounting-system-with-excel-and-power-query-p3/)**

### Recap of our Accounting System
In the previous post, we have created the Balance Sheet report for our accounting system. In this post we're going to create the AR & AP Aging reports, load the reports to our worksheets, and finally do some finishing touches to our system to make it more user-friendly. Let's get started.

### Creating the AR & AP Aging Reports
The **AR Aging Report** shows the outstanding receivables of the company and for how long it is already outstanding. The **AP Aging Report** is likewise the same but for the payables instead. I'm going to show only how to create the AR Aging Report as the same steps can be applied to arrive at the AP Aging Report.

1. To start, create a blank query and get the data from the **3_GL complete** query.
2. Remove unnecessary columns such as the "Supplier", "Source", "Payee", "Account #". 

    ![AR Aging Start](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_aging_start.png)

3. Now, filter the Account column to include only Accounts Receivable. Sort the Date in ascending order

    ![AR Aging Sorted](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_aging_sorted.png)

4. Looking at the data above, we could see that we need to get earliest date to compute the days outstanding and at the same time get the sum of the amounts for each invoice. For example, the invoice date for **INV10004** is Sept 13, 2010 and the outstanding balance is $500. We could do this with the GroupBy function.

    Go to **Transform > GroupBy** and input as follows

    ![AR Aging Groupby](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_aging_groupby.png)

    The above will result to the following. Take note that the GL data should be completed properly in order for this to work.

    ![AR Groupby Result](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_groupby_result.png)

5. Now create an additional column showing the Days oustanding. Go to **Add Column tab > Custom Column**

    ![AR datetime](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_datetime.png)

    `DateTime.LocalNow()` is the formula for the current date and time.
    <br>
    <br>

6. The resulting value is a `duration` object. We need to extract the days only. Select the Days Outstanding column and go to **Transform > Duration > Days**. Our data now looks like this
    
    ![AR duration](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_duration.png)

7. Reorder the columns with the Invoice Date at the first column.
    
    ![AR final](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_aging_final.png)

    Name the query as **9_AR Aging**. Close and load as connection only.

Take the same steps to do the AP Aging report. Name the AP Aging as **10_AP Aging**. Close and load it too as connection only.

### Loading the Reports to the Worksheets
We now have all the necessary reports needed in our accounting system. Let's load them now in our workbook. Create the following worksheets after the General Ledger sheet

* AR Aging
* AP Aging
* TB
* P&L
* BS

Load the reports to the corresponding worksheets. Right click on the query and choose **Load To..**. For example

![AR aging load](/img/creating-a-simple-accounting-system-with-excel-and-power-query/ar_aging_load.png)

Do this for all the reports you wish to load in the worksheets.

### Finalizing our Accounting System

Alright, let's wrap up our accounting system and make it more user-friendly. Thing we're going to do are:

* Menu sheet
* Drop-down autocomplete
* Button to refresh all reports

#### Menu Sheet
This will serve as shortcuts to our worksheets for quick navigation. It could be simple as follows

![Menu](/img/creating-a-simple-accounting-system-with-excel-and-power-query/menu.png)

The trick is to add hyperlinks pointing to each worksheet as follows

![Hyperlinks](/img/creating-a-simple-accounting-system-with-excel-and-power-query/hyperlinks.png)

In addition, provide a hyperlink to quickly return to the Menu sheet from any worksheet. For example, the following contains the link to return to the Menu from P&L

![Back to Menu](/img/creating-a-simple-accounting-system-with-excel-and-power-query/back_to_menu.png)

#### Drop Down Autocomplete
If you try to enter another transaction in the General Ledger, you would notice that you have to scroll thru the Accounts listing when entering the account name to use. The placeholder for our new accounts in AccountsT table are also being shown as blank spaces.

The same is happening with our Customer and Supplier columns drop downs. This could be very problematic if we have a lot of accounts, customers, or suppliers in our system. Luckily, there's a free Excel add-in to solve this. It adds a autocomplete functionality to our existing data validation rules.

1. Go to this [link](https://sites.google.com/site/beyondexcel/project-updates/comboboxdatavalidationadd-in) and download the Excel add-in. Place the *.xlam* file in the same folder as the accounting system file for simplicity.

2. Now, load the add in Excel. In Excel 2016, you could do this by going to the **File menu > Options > Add-ins**.
    
    ![Load Addin](/img/creating-a-simple-accounting-system-with-excel-and-power-query/load_addin.png)
    
4. Choose the **Excel Add-ins** from drop-down and click **Go**. It will show you the add-ins dialog box. Since it's your first time to load the add-in, you must browse for the location of the *.xlam* file. Make sure it is selected after
    
    ![Addins dialog](/img/creating-a-simple-accounting-system-with-excel-and-power-query/addins_dialog.png)

5. If successful, a new tab ComboBox will appear in your Excel window. Click on the ComboBox checkbox to activate it

    ![Activate combobox](/img/creating-a-simple-accounting-system-with-excel-and-power-query/activate_combobox.png)

6. Now, you would notice that our data validation dropdown is now a search box. Typing the name of the account will give you suggestion on the account to use. Press **Tab** to select the account

    ![GL Autocomplete](/img/creating-a-simple-accounting-system-with-excel-and-power-query/gl_autocomplete.png)

    When done using the add-in, you may deactivate it in the ComboBox tab.

#### Refresh button
Finally, let's provide a button to refresh all our queries.

1. Go to Menu sheet and add a button using shapes like the following

    ![Refresh button](/img/creating-a-simple-accounting-system-with-excel-and-power-query/refresh_button.png)

2. Go to **Developer tab > Visual Basic**. This will open the VB editor. Add a new module and paste the following code.

    ```
    Public Sub UpdatePowerQueriesOnly()
    Dim lTest As Long, cn As WorkbookConnection
    On Error Resume Next
    For Each cn In ThisWorkbook.Connections
    lTest = InStr(1, cn.OLEDBConnection.Connection, "Provider=Microsoft.Mashup.OleDb.1")
    If err.Number <> 0 Then
    err.Clear
    Exit For
    End If
    If lTest > 0 Then cn.Refresh
    Next cn

    MsgBox "Data has been updated!"
    End Sub
    ```
    
    The above code basically refreshes all PowerQuery queries.

3. If prompted to save the file as macro-free workbook, click **No** and save the file as *.xlsm* file.

4. Then right click on the button and choose **Assign Macro**. Choose the macro that you've just created.
    
    ![Assign Macro](/img/creating-a-simple-accounting-system-with-excel-and-power-query/assign_macro.png)

5. Finally, test the button if it works. It should show a messagebox when all the queries are updated

    ![Messagebox](/img/creating-a-simple-accounting-system-with-excel-and-power-query/messagebox.png)

### Conclusion

Hooray! We have finish our simple accounting system using Excel and Power Query. As a recap, we have

* leveraged tables, named ranges, & data validation to build the basic functionality of our system.
* used PowerQuery to create easily update financial reports
* used Hyperlinks for navigation in our system
* and finally, utilized free add-ins and used VBA to add autocompletion and refresh button to our system.

That's it for now. Watch out for my other tutorials in this blog to make you an awesome accountant using Power Query.

Till then, **Stay Querious & Happy coding!**


