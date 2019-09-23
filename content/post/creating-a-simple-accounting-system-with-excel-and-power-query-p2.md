---
title: "Creating a Simple Accounting System with Excel and Power Query - Part 2"
linktitle: "Creating a Simple Accounting System with Excel and Power Query - Part 2"
date: 2019-09-29
draft: true
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
prev: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p1/
next: 
---

## Creating a Simple Accounting System with Excel & Power Query - Part 2
<br>

This is the **Part 2** of the series **Creating a Simple Accounting System with Excel & Power Query**.
If you missed the **Part 1**, you can read it **[here](../creating-a-simple-accounting-system-with-excel-and-power-query-p1/)**


### Recap of our Accounting System
In the previous post, we have created the meat of our accounting system using tables, name ranges, and data validation. It basically has the following components:

* Chart of Accounts
* Customer list
* Supplier list
* General Ledger

In this post, we're going to incorporate Power Query in order to create reports from our accounting system that are easy to update. Our system can be only more relevant to our users if they could produce reports that they can used to make business decisions. 

### Creating the General Ledger
First, we have to recreate our General Ledger. Why do we have to do it? Basically, we want to add another column in our General Ledger to specify the type of each accounts. The account types will be our basis to aggregate transactions in our ledger and subsequently present them in our Balance Sheet or P&L Statements.

For example, supposed we have 10 Sales account, one for product (ie. Sales - Product1, Sales - Product2, etc.). To present all of these 10 accounts in our P&L would be messy and inappropriate. We're creating more noise than the signal in our report. Instead, what we present is usually the sum of all these sales account which we do by the account type.

To start, go to the General Ledger sheet, then go to Data tab > 

### Conclusion
In this tutorial, we have the beginning of an accounting system. We have used familiar tools in Excel like tables, named ranges, and data validation to do just that. In the next post, we're going to discuss how to use Power Query in order create reports from our data and easily refresh them with a click of a button. This is great for real time monitoring financials of a small business.

Till then, **Stay Querious & Happy coding!**


