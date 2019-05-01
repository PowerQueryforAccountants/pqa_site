---
title: "Summarizing Time Logs for Payroll"
linktitle: "Summarizing Time Logs for Payroll"
date: 2019-03-23
tags: [
    "groupby",
    "fifo.costing",
    "time.sheets"
]
archives: ["2019"]
prev: /post/detecting-unrecorded-sales/
next: /post/cleaning-contacts-lists-for-import/
---

### Summarizing Time Logs for Payroll
<br>

Biometrics systems are now ubiquitous in offices. These devices work by allowing entry and exit to office promises usually with the use of fingerprint scan. These scans happen a lot during the day as employees arrive at the office, take a lunch or a restroom break, and upon leaving the office.

Now the challenge is to summarize these time logs in order to get the hours worked by each employee at the end of each payroll period.
We could do these easily with Power Query.

### The Process

To follow along, you could download the files **[here](https://github.com/PowerQueryforAccountants/Summarizing-Time-Logs-for-Payroll)**.

Our file is a typical output from biometric devices. In contains all the instances where an employee use the the device to get in and out of the office. 

To get the hours worked for each employee each day, we have to group employee time logs by employee number and date. From this grouping, we're going to get the first time record and last time record. The difference between the two will be hours worked by each employee each day.

To do this in Power Query

1. Create a new workbook name **Time Summary.xlsx**. Create a new query from a the workbook and choose the **Time Logs.xlsx**. Promote the first row to headers.
2. Since we we're going to group time logs by User Number and Date we have to create a new column that will concatenate these two. Create first a copy of the **Date/Time** column and change its type to *Date*. Rename it also as **Date** column.
    
    The data should now look like this

    ![Date Column](/img/summarizing-time-logs-for-payroll/date_col.png)
    <br/>
    <br/>

3. We could now create the custom column to combine the **User Number** and **Date** columns. Concatenation works only for Text type data hence we have to convert the columns to combine to **Text** type first. Go to **Add Column tab > Custom Column** to add this column

    ![User Date](/img/summarizing-time-logs-for-payroll/user_date.png)
    <br/>
    <br/>

4. Now, we could group the data by **UserDate** column and get the first and last time record.

    Note that the **Sum** operation will not provide us with the time record we want, but will serve as scaffold to our code. Go to the **Advanced Editor** and change the code from this
    ```
    Table.Group(#"Sorted Rows", {"UserDate"}, {{"FirstIn", each List.Sum([#"Date/Time"]), type datetime}, {"LastOut", each List.Sum([#"Date/Time"]), type datetime}}),
    ```

    To this
    ```
    Table.Group(#"Sorted Rows", {"UserDate"}, {{"FirstIn", each List.First([#"Date/Time"]), type datetime}, {"LastOut", each List.Last([#"Date/Time"]), type datetime}}),
    ```
    The table should now look like this

    ![First In Last Out](/img/summarizing-time-logs-for-payroll/filo.png)
    <br/>
    <br/>

5. Change the data type for **FirstIn** and **LastOut** columns to **Time** so that it will show only the time component.
    
    ![Time Format](/img/summarizing-time-logs-for-payroll/to_time.png)
    <br/>
    <br/>

6. Add another column named **HoursWorked** to get the difference between the **FirstIn** and **LastOut**. This is the duration that the employee stayed in the office.

    ![Hours Worked](/img/summarizing-time-logs-for-payroll/hours_worked.PNG)
    <br/>
    <br/>

7. To account for lunch break, add another column and deduct hours alloted for lunch from the **HoursWorked** column. Named this new column **Adjusted Hours Worked**.

    ![Adjusted Hours Worked Column](/img/summarizing-time-logs-for-payroll/adj_hours_worked_col.PNG)

    The data should now look like this

    ![Adjusted Hours Worked](/img/summarizing-time-logs-for-payroll/adj_hours_worked.PNG)
    <br/>
    <br/>

8. Finally, add back again the **User Number** and **Date** columns by extracting it from the UserDate column. Delete unnecessary columns as necessary.
    The final data should look like this.

    ![Final Summary](/img/summarizing-time-logs-for-payroll/final_summary.PNG)
    <br/>
    <br/>

### Conclusion
In this tutorial, you have learned how to use Power Query's group feature in order to summarize time logs for payroll purposes. You have learned how to deal with Date and Time datatypes and making use of them properly when making calculations.

You could reuse this workbook repeatedly for future payroll. Point the workbook to a new file, refresh the query, and you're good to go.

That's it for now. Check out other post here to learn more about Power Query.

**Stay Querious. Happy coding!**