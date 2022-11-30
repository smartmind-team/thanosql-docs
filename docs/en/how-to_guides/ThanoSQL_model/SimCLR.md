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
    - __literal__: a fixed or unchangeable value, also known as a Constant.
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

- "image_col": sets the name of the column containing the image path. (default: "path")
- "filename_col": sets the name of the column containing the image file name. (default: "file_name")
- "label_col": sets the name of the column containing the path of the label (default: "label")
- "max_epochs": sets how many times the dataset is trained in total. (default: )
- "batch_size": the size of the dataset bundle read during a single train. (default: 256)
- "overwrite": overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (default: False)

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

- "table_name": sets the name of the table to store the new vector results

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
SEARCH IMAGE 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS (
    (search_input_type = {image | text | audio | video}),
    [search_input = expression],
    [emb_col = embedded_column_name]
    [column_name = column_name_to_be_saved_as]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "search_input_type": defines the image|text|audio|video type to be used for the search.
- "search_input": defines the input to be used for the search. 
- "emb_col": the column that contains the vectorized results.
- "column_name": defines the name of the column that contains the search results. (default: "search_result")


__SEARCH Example__

Examples of SEARCH queries can be found in [Search Image by Image](/en/tutorials/thanosql_search/search_image_by_image/).

```sql
%%thanosql
SEARCH IMAGE 
USING my_image_search_model
OPTIONS (
    search_input_type="image",
    search_input="tutorial_data/mnist_data/test/923.jpg",
    emb_col="convert_result",
    column_name="search_result"
)
AS
SELECT *
FROM mnist_test
```
