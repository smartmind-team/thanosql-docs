---
title: SimCLR
---

# __SimCLR__

__Notation Conventions__

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces `{}` are used to bind combinations of options.
- The bracket `[]` indicates an optional clause.
- An ellipsis following a comma in brackets [,...] means that the preceding item can be repeated as a comma-separated list
- The vertical bar `|` represents the logic `OR`.
- VALUE means just value.

!!! note ""
    __literal__ : A fixed or unchangeable value, also known as Constant.
    > Each literal has a special data type such as column, in the data table.


## __BUILD MODEL Query Syntax__

Use this "__BUILD MODEL__" query syntax to develop an AI model.
The "__BUILD MODEL__" expression allows you to learn defined data sets through query_expr after "__AS__".
​

```sql
BUILD MODEL (model_name_expression)
USING SimCLR
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

​
__OPTIONS Clause__
​

```sql
OPTIONS(
    (image_col = VALUE),
    (filename_col = VALUE),
    [label_col = VALUE],
    [max_epochs = VALUE],
    [batch_size = VALUE],
    [overwrite = {True | False}]
)
```

​
The "__OPTIONS__" clause allows you to change the values of parameters in the SimCLR quantization model from their default values. The meaning of each parameter is as follows.

- "image_col" : Sets the path of the image in the data table. (DEFAULT : "path")
- "filename_col" : Sets the column containing the image file name in the data table. (DEFAULT : "file_name")
- "label_col" : Column containing image label. (DEFAULT : "label")
- "max_epochs" : Sets the number of model learnings (DEFAULT : 5)
- "batch_size" : Sets the number of data in a bundle of data used during learning. (DEFAULT: 256)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

__Example BUILD MODEL Query Syntax__

You can find examples of using the algorithm query syntax in [Search Image as Image](/en/tutorials/thanosql_search/image_search/search_image_by_image/).
​

```sql
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="image_path",
    max_epochs=1,
    overwrite=True
    )
AS
SELECT *
FROM mnist_train
```

## __CREATE TABLE Query Syntax__

This "__CREATE TABLE__" query syntax enables digitization conversion using image folder paths without a table containing image-specific folder path information.
The "__CREATE TABLE__" expression quantifies the image files in the image folder path after "__FROM__" and stores them as a table.
​

```sql
​
CREATE TABLE (table_name_expression) 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expr)
```

​
__OPTIONS Clause__
​

```sql
OPTIONS(
    (path_type = {'folder'|'file'}),
    (data_type = {'image'|'audio'|'video'}),
    (file_type = LIST),
    [overwrite = {True | False}]

    )
```

​
"__OPTIONS__" defines the attribute values of the image file for image quantification. The meaning of each parameter is as follows.

- "path_type" : Sets the type of file path where data is stored (folder|file)

- "data_type" : Sets the type of unstructured data you enter. (image|audio|video)

- "file_type" : Defines the extension of the destination file as a list (ex. ['.jpg'], '['.png'])
  ​
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

## __CONVERT USING Query Syntax__

Use this "__CONVERT USING__" query syntax to store the digitized results as a new column in the existing dataset using a dataset containing the paths of existing images. Each time a new quantization model is used, a new quantization column is added to facilitate comparison by quantification results.
​

```sql
CONVERT USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (table_name = VALUE)
    )
```

The "__OPTIONS__" clause allows you to change the values of parameters in the SimCLR quantization model from their default values. The meaning of each parameter is as follows.
​

- "table_name" : Sets the name of the table to store the new digitization results

__Example of CONVERT USING query syntax__

You can find examples of using the algorithm query syntax in [Search Image as Image](/en/tutorials/thanosql_search/image_search/search_image_by_image/).
​

```sql
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="image_path"
    )
AS
SELECT *
FROM mnist_test
```

## __SEARCH IMAGE Query Syntax__

You can use the "__SEARCH IMAGE__" query syntax to retrieve the desired image from the table that generated the digitization.

```sql
SEARCH IMAGE (images = expression)
USING (model_name_expression)
AS
(query_expr)
```

!!! note ""
    You must receive images as input from the user. The input must be a string (for example, 'a black cat', 'data/image/image01.jpg')

__Example SEARCH IMAGE Syntax__

You can find examples of using the algorithm query syntax in [Search Image as Image](/en/tutorials/thanosql_search/image_search/search_image_by_image/).

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg'
USING my_image_search_model
AS
SELECT *
FROM mnist_test
```
