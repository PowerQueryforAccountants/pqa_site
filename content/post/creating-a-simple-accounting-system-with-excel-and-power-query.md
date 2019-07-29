---
title: "Creating a Simple Accounting System with Excel and Power Query"
linktitle: "Creating a Simple Accounting System with Excel and Power Query"
draft: true
date: 2019-08-03
tags: [
    "accounting.system",
    "data.validation",
    "general.ledger",
    "trial.balance",
    "balance.sheet",
    "income.statement"
]
archives: ["2019"]
prev: /post/audit-procedures-in-power-query-fraud-detection/
next:
---

### Creating a Simple Accounting System with Excel & Power Query
<br>

outline:
* planning the system 
    books needed
        Accounts Table
        General Ledger table - actually more like a General Journal table - debits are positive, credits are negative
        Customer
        Supplier

    reports needed
        trial balance
        balance sheet
        income statement

* Start with the Chart of accounts
    Start with 1 - Asset
    Start with 2 - Liability
    Start with 3 - Equity
    Start with 4 - Revenue
    Start with 5 - Expenses
    
    There could be no duplicate in the Account Name - in database parlance - this is the primary key

* Then create the Customers and Suppliers Table
    Let's assume for now that the Name is the primary key (though in reality, this is not the case)

* Then finally create the General Ledger table
    This is like the General Journal though it is not necessary that transactions are entered chronologically as long as each has associated transaction date
    Utilize data validation for the Account Name, Customer Name, Supplier Name

* Enter some transactions

* Create merge report - Accounts_T and GL_T

* Create reports TB, Income Statement, Balance Sheet - BS is last cause we need the NI/NL figure

* Create the AR aging and AP aging report

* Bonus: Create Account Transaction Report

* Bonus: Use Autocompletion plugin


TODO BONUS:
    The use of autocomplete for data validation
    Account Transaction with the use of Data Model
