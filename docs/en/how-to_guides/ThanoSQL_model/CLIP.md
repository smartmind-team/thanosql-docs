---
title: CLIP
---

# __CLIP__

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

## __CONVERT Syntax__

The "__CONVERT__" statement converts image data from an existing table into a vector and adds it to the table.

```sql
CONVERT USING clip_en
OPTIONS(
    (table_name = expression),
    (image_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (table_name=expression),
    (image_col=column_name),
    [batch_size=VALUE]
)
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "table_name": the name of the new table to be created.
- "image_col": the name of the column containing the image path. (default: 'image_path')
- "batch_size": the size of the dataset bundle read during a single train. (default: 16)

__CONVERT Example__

Examples of CONVERT queries can be found in [Image search with text](/en/tutorials/thanosql_search/search_image_by_text/).

```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path",
    table_name="unsplash_data",
    batch_size=128
    )
AS
SELECT *
FROM unsplash_data
```

## __SEARCH Syntax__

You can use the "__SEARCH__" statement to retrieve the desired image from the table that generated the vectors.

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
    (search_input_type = {image | text | audio | video | keyword}),
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

Examples of SEARCH queries can be found in [Image search with text](/en/tutorials/thanosql_search/search_image_by_text/).

```sql
%%thanosql
SEARCH IMAGE 
USING tutorial_search_clip
OPTIONS (
    search_input_type="text",
    search_input="a black cat",
    emb_col="convert_result",
    column_name="search_result"
)
AS
SELECT *
FROM unsplash_data
```
