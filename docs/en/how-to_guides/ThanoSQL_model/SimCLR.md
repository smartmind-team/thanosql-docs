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
- VALUE represents a regular value.

!!! note ""
    - __literal__ : A fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.


## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" query to develop an AI model.
The "__BUILD MODEL__" statement allows you to train datasets defined with the query_expr that comes after the "__AS__" clause.
​

``` sql
query_statement:
    query_expr

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
The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "image_col" : Sets the name of the column containing the image path. (default: "path")
- "filename_col" : Sets the name of the column containing the image file name. (default : "file_name")
- "label_col" : Sets the name of the column containing the path of the label (default : "label")
- "max_epochs" : Sets how many times the dataset is trained in total. (default : )
- "batch_size" : The size of the dataset bundle read during a single train. (default: 256)
- "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (default: False)

__BUILD MODEL Example__

Examples of BUILD MODEL queries can be found in [Image search with image](/en/tutorials/thanosql_search/search_image_by_image/).
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

## __CONVERT Syntax__

Use the "__CONVERT__" statement to store the vector results as a new column of the existing dataset using a dataset containing the paths of the existing images. Each time a new model is used, a new vector column is added for comparison of vectorization results.
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

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.
​

- "table_name" : Sets the name of the table to store the new vector results

__CONVERT Example__

Examples of CONVERT queries can be found in [Image search with image](/en/tutorials/thanosql_search/search_image_by_image/).
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

## __SEARCH Syntax__

You can use the "__SEARCH__" statement to search an image from the vector table.

```sql
SEARCH IMAGE (images = expression)
USING (model_name_expression)
AS
(query_expr)
```

!!! note ""
    - Images must be inputted as a string (for example, 'a black cat', 'data/image/image01.jpg').

__SEARCH Example__

Examples of SEARCH queries can be found in [Image search with image](/en/tutorials/thanosql_search/search_image_by_image/).

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg'
USING my_image_search_model
AS
SELECT *
FROM mnist_test
```
