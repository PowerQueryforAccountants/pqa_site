---
draft: true
title: "Creating a Simple Accounting System with Excel and Power Query - Part 1"
linktitle: "Creating a Simple Accounting System with Excel and Power Query - Part 1"
date: 2019-09-22
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
prev: /post/audit-procedures-in-power-query-fraud-detection/
next: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p2/
---

## Creating a Simple Accounting System with Excel & Power Query - Part 1
<br>

In this series, we're going to explore the use of Power Query in creating a simple accounting system.

Excel has always been the go-to tool for accountants. It's so simple and easy to use which makes it ideal for storing financial data. Its real power comes from its flexible yet powerful features.

Now, with the availability of Power Query, we could now extend Excel easily to create a simple accounting system.

To follow along, you could download the exercise files from **[here](https://github.com/PowerQueryforAccountants/Accounting-System-with-Excel-and-PQ)**.

### Planning the System
Before we dive in and create our accounting system, let's think of the different parts and components of our system. For a simple accounting system, we basically need the following:
    
* **Accounts table** - this will contain our Chart of Accounts
* **Customers table** - this will contain our list of Customers and their details
* **Suppliers table** - this will contain our list of Suppliers and their details
* **General Ledger table** - this will contain our transactions and the necessary details

We also have to think of the reports that we're going to need. These are the following:

* **Trial balance** - a collection of the accounts and their balances
* **Balance sheet**
* **Income statement**

We now have an idea on what our accounting system would need and the report in can produce. 

Next, we're going to create our Accounting system starting with the Chart of Accounts.

### Start with the Chart of Accounts
1. In a new workbook, blank worksheet and name it "Accounts". Create a table in the worksheet with the following headings

    Account Name<br>
    Account #<br>
    Account Type<br>

    Name the table **AccountsT** (T meaning "table"). Rename also the worksheet as "Accounts"
    <br>
    <br>

2. Input the Chart of Accounts you're using into the AccountsT table. For reference, you could copy the Chart of Accounts used in the exercise files [here](https://github.com/PowerQueryforAccountants/Creating-a-Simple-Accounting-System-with-Excel-and-Power-Query). The AccountsT should like this:

    ![Accounts Table](/img/creating-a-simple-accounting-system-with-excel-and-power-query/accounts.png)
    <br>
    <br>


    Any additional Accounts should be added to our Accounts Name column. In addition, new accounts should have the appropriate account numbers. For our purposes, account # should start with the following number

    1 - Asset<br>
    2 - Liability<br>
    3 - Equity<br>
    4 - Revenue<br>
    5 - Expense<br>

    Take note that there could be no duplicate account names in this table. In databse terminology, the Account Name column is our primary key.
    It is the column that uniquely identifies each row. 

    Next, we create our Customers and Suppliers table.

### Creating the Customers and Suppliers table
1. In the same workbook, create a table in a new worksheet with the following headings:

    Customer Name<br>
    Contact Person<br>
    Contact Person Title<br>
    Email<br>
    Mobile No.<br>
    <br>
2. Named the table **CustomersT**. Rename also the worksheet as "Customers"

3. For the Suppliers table, we follow the almost the same steps 1 and 2. Use the following headings:

    Supplier Name<br>
    Contact Person<br>
    Contact Person Title<br>
    Email<br>
    Mobile No.<br>
    <br>
4. Name the table **SuppliersT** and the worksheet as "Suppliers"

5. As start, input the following in the names column of CustomersT and SuppliersT.

    Example Co.<br>
    JD Co<br>
    Arlene Co<br>
    Kim Co<br>
    Mau Co<br>
    Lae Co<br>
    Noemi Co<br>
    Jasha Co<br>
    Kristine Co<br>
    Pearl Co<br>
    Ivy Co<br>
    <br>
    This will serve as our initial list of customers and suppliers. Don't worry if there are no other details for now.

    Next, let's proceed with the General Ledger

### Creating the General Ledger
We're now in the main table for our accounting system. The **General Ledger** (hereafter **GL**) will house the transactions and relevant details. To start:

1. Create a table named **GeneralLedgerT** in a new worksheet with the following headings

    Date (MM/DD/YYYY)<br>
    Ref #<br>
    Customer<br>
    Supplier<br>
    Source<br>
    Account<br>
    Payee<br>
    Description<br>
    Amount<br>

    Named the worksheet "General Ledger"
    <br>

2. The columns Customer will contain our Customer names. To prevent anyone from misspelling the names, let's use data validation. Take note that we have to consider future additions to our customer names in our data validation. Thankfully, this is easy cause we have formatted our Customers data as a table. 
    
    * Create first a named range for the Customer Name column of the CustomersT table. Go to Formulas tab > Name Manager > New. Create a named range as follows

        ![customers range](/img/creating-a-simple-accounting-system-with-excel-and-power-query/customers_rng.png)
        <br>
        <br>

    * Next, select the General Ledger worksheet. Highlight the first 500 rows of the Customer column then go to Data tab > Data validation and input the following as data validation.

        ![customers validation](/img/creating-a-simple-accounting-system-with-excel-and-power-query/customers_validation.png)
        <br>
        <br>

    Take note that I've only created the validation for the first 500 entries to our GL to test it. You may do the validation on more records if you wish. The *Ignore blank* option is important so as to have error warnings if we entered transactions without a customer like this:

    ![error validation](/img/creating-a-simple-accounting-system-with-excel-and-power-query/error_validation.png)
    <br>
    <br>

3. The Supplier and Account column will also need data validation but the process is the same with the Customer column. Namely:

    * Define a named range for the Supplier Name and Account Name.
    * Implement the validation using the name ranged above in the General Ledger table.

    You may refer to the details steps in #2 to do the data validation for Supplier and Account names.
    <br>

4. From the exercise files, copy the contents of the **Transactions.xlsx** to your General Ledger sheet. This will serve as initial set of transactions to based our reports later on.

In the next post, we're going to start creating our reports and leverge the Power Query to do just that.

### Conclusion
In this tutorial, we have the beginning of an accounting system. We have used familiar tools in Excel like tables, named ranges, and data validation to do just that. In the next post, we're going to discuss how to use Power Query in order create reports from our data and easily refresh them with a click of a button. This is great for real time monitoring financials of a small business.

Till then, **Stay Querious & Happy coding!**


