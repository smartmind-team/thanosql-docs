# __Query Manager Manual__

The Query Manager provides a familiar UI similar to DBMS, allowing you to execute ThanoSQL queries and analyze the results. It provides an easy way to understand the schema and table structure of the workspace database, and facilitates the management of previous query results through Query Logs. Additionally, it offers a Data Viewer for analyzing unstructured data such as images and videos in the query execution results.

**Main Features**

- Execution of ThanoSQL and basic SQL commands
- Retrieval of data table schema information/preview
- Query Log
- Data Viewer

## __Overview of the Query Manager Page__

The Query Manager has three main sections:

[![IMAGE](/en/img/getting_started/paas/workspace/qm_img_0.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/qm_img_0.png)

① Query Editor

② Database Explorer

③ Query Log

### __Query Editor__

Execute a ThanoSQL query and view the results as a table.

[![IMAGE](/en/img/getting_started/paas/workspace/qm_img_1.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/qm_img_1.png)

① Tab : You can write queries and view results in separate tabs.

② Query Editor : You can write and execute ThanoSQL or basic SQL queries. It supports [Keyboard Shortcuts](#keyboard-shortcuts).

③ CSV Download : You can download the results of the query as a CSV file.

④ [Data Viewer](#data-viewer) : This feature allows you to analyze unstructured data, such as images and videos.

⑤ Result Table : The query results are displayed in a table format.

#### Data Viewer

The Data Viewer feature allows you to view unstructured data within the result table. You can select a column that contains the path to the unstructured file and choose the file format (image, video, audio) to display the unstructured data. The pagination feature at the bottom allows you to adjust the number of items displayed per page and navigate to different pages.

!!! warning
    The Data Viewer displays unstructured data based on the file path. Therefore, the stored path value must actually exist in the workspace. (Sometimes it may not appear due to issues such as proxies.)

#### Keyboard Shortcuts

The following keyboard shortcuts are supported in the Query Editor:

| Action | Key Combination (Windows/Linux) | Key Combination (Mac OS) |
| -- | -- | -- |
| Run current query or selected query | Ctrl+Enter | Cmd + Enter |
| Autocomplete current word | Tab or Ctrl + Space | Tab or Cmd + Space |
| Comment current or selected lines | Ctrl+/ | Cmd + / |
| Open the menu of the editor | Shift + F10 | Shift + F10 |
| Open the list of shortcuts for the editor | F1 | F1 |

!!! tip
    The Query Editor is using the Monaco Editor, which is also used in VSCode. Most of the shortcuts are the same as those used in VSCode. For a list of shortcuts for Monaco Editor, refer to [link](https://en.docs.monaca.io/products_guide/monaca_ide/editor).

### __Database Explorer__

You can check the workspace DB [Schema](https://www.postgresql.org/docs/current/ddl-schemas.html) and table configuration information. Each workspace basically provides `public` and `qm` schemas, and all queries that do not specify schema refer to `public` tables. Among the queries executed in the query editor, all queries whose results are in the form of a table are stored in the `qm` schema as an arbitrary table name. Using this, previous table execution results can be reused at any time.

!!! warning
    Query result temporary tables in schema `qm` are only kept for a maximum of 7 days.

#### **Table Information**

The Database Explorer provides information about tables within a schema. When you click on a table name, you can access the following information:


**Table Schema**

You can check the column names, data types, and whether null values are allowed for the column.

[![IMAGE](/en/img/getting_started/paas/workspace/img3.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img3.png)

**Preview**

You can quickly understand the contents of the data table in question.

!!! warning
    Editing of the data table is not allowed in the Preview.

[![IMAGE](/en/img/getting_started/paas/workspace/img4.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/img4.png)

### __Query Log__

All queries executed in Query Manager are logged and displayed in the query log section. They are listed in the order of the query creation time, showing the query statement and its execution status.

[![IMAGE](/en/img/getting_started/paas/workspace/qm_img_2.png){: style="max-height:none"}](/en/img/getting_started/paas/workspace/qm_img_2.png)

#### Details Window

When you click on a query log, detailed information about the query is displayed on the right side.

① Query Information : It displays the query ID, execution status, creation time, execution time, completion time, and the target table name.

② Query Statement : The executed query statement is shown.

③ Error Message : If an error occurred during query execution, the error message is displayed.

④ Open as New Query : It opens a new query editor tab with the query statement.