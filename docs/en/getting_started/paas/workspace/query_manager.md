---
title: Query Manager
---

# __Query Manager Manual__

Query Manager is a tool that allows you to execute ThanoSQL commands and analyze their results. The key features are as follows:

- Execution of ThanoSQL and basic SQL commands
- Retrieval of data table schema information/preview

## __1. Creating a New Query__

- Click the **New Query** button to create a query editor tab.

[![IMAGE](/en/img/getting_started/paas/workspace/img1.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img1.png)

## __2. Executing a Query__

- Write and execute a query to view the results.

[![IMAGE](/en/img/getting_started/paas/workspace/img2.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img2.png)

① Query Input Window

- Write ThanoSQL queries in this window.

② Query Execution Button

- Execute the query.

③ Query Execution Result Window

- The query execution results are displayed in tabular form.

④ Pagination

- You can adjust the number of rows displayed at a time (maximum 100 rows).
- You can move to the next/previous page and view the current page number.


## __3.  Data Table Information__

Using the Database Navigation on the left-hand side, you can see the table configuration information for each [schema](https://www.postgresql.org/docs/current/ddl-schemas.html). In ThanoSQL, the public and qm schemas are provided by default, and the query results executed in Query Manager are stored in the qm schema with an arbitrary table name. You can reuse previous table execution results at any time by using this feature.

In addition, clicking on a table name will display the following information:

### __3-1. Table Schema__

You can check the column names, data types, and whether null values are allowed for the column.

[![IMAGE](/en/img/getting_started/paas/workspace/img3.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img3.png)

### __3-2. Preview__

You can quickly understand the contents of the data table in question.

!!! warning
    Editing of the data table is not allowed in the Preview.

[![IMAGE](/en/img/getting_started/paas/workspace/img4.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img4.png)