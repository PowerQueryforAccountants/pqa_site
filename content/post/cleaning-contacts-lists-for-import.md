---
title: "Cleaning Contacts List for Import to Xero"
linktitle: "Cleaning Contacts List for Import to Xero"
date: 2019-03-09
draft: true
tags: [
    "List.Accumulate",
    "groupby",
    "merge",
    "join"
]

archives: ["2019"]
---

### Cleaning Contacts List for Import to Xero
<br>

There are times when you want to bulk import Contacts to Xero from an external system like a CRM. The problem is that CRM reports usually don't comply with the import format required by Xero.

In this tutorial, we're going to use Power Query in order to simplify the process of transforming our Contacts lists in a format that is suitable for format. You could download the file here and follow along.

### Let's review our data
Our data is basically formatted like this:


You would notice too that the address is split into several lines. Moreover, the Phone, Mobile, and Email are labeled within the data itself. We have to separate these labels and make them as column headings.

### The process
Basically, we have to divide our data into three parts that we're going to "stitch" later on using the Code column to arrive at the final transformed dataset.

First, in a blank workbook, create a query and choose the Contacts.xlsx file. Click on Edit to do preliminary cleaning. Basically we have to remove the first blank row and promote the second row as headers. Copy this dataset 3 times. In effect, we're going to have 4 queries. We're going to change these 3 copies and the original one will retain as our back up.

In the first query, filter the first row to remove rows with null values. That's it for the first part. Rename this query as Full Name.

In the second query, follow the following steps:
1. Copy the Code column
2. Fill down this column
3. Remove the second column.
4. Filter the original code and this time retain rows with null values. The resulting table would be like this.
5. Now, extract all the characters after the column in Contact Details column. Extract also the characters before the column. In effect, we are splitting this column by the colon.
6. Now, let's Pivot the table. Pivot basically flips the data where a values of a chosen column will be column names and values of another column would be the values for those new columns. We want the Phone, Mobile, and Email as new columns.