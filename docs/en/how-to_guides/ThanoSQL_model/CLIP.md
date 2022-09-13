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
- VALUE means just value.

!!! note ""
    __literal__ : A fixed or unchangeable value, also known as Constant.
    > Each literal has a special data type such as column, in the data table.


## __CREATE TABLE Query Syntax__

You can use the syntax "__CREATE TABLE__" to generate a data table containing the quantization vectors of the image data.

```sql
CREATE TABLE (table_name_expression)
USING clip_en
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expresison)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (path_type = column_name),
    (data_type = column_name),
    [file_type = VALUE],
    [batch_size = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in CLIP from its default value. The meaning of each parameter is as follows.

- "path_type" : Sets the column containing the path of the audio files in the data table (DEFAULT: "audio_path")
- "data_type" : Type of data.
- "file_type" : The format of the extension of the image.
- "batch_size" : The size of a bundle of data sets read from one prediction. (DEFAULT : 16)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

## __CONVERT USING Query Syntax__

The "__CONVERT USING__" query syntax converts image data from an existing table into a quantized vector and adds it to the data table to use.

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

The "__OPTIONS__" clause allows you to change the value of a parameter from its default value in the model. The meaning of each parameter is as follows.

- "table_name" : The name of the new table to be created.
- "image_col" : The name of the column containing the path of the image in the table. (DEFAULT : 'image_path')
- "batch_size" : The size of a bundle of data sets read from one prediction. (DEFAULT : 16)

__Example of CONVERT USING syntax__

You can find examples of using the algorithm query syntax in [search by text](/en/tutorials/thanosql_search/image_search/search_image_by_text/).

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

## __SEARCH IMAGE Query Syntax__

You can use the "__SEARCH IMAGE__" query syntax to retrieve the desired image from the table that generated the digitization.

```sql
SEARCH IMAGE ({text|texts|image|images} = expression)
USING clip_en
AS
(query_expr)
```

!!! note ""
    You must receive one of text, texts, image, and images as input. Text and texts, image, and images are the same. The input must be string (e.g., 'a black cat', 'data/image/image01.jpg') or list of strings (e.g., ['a black cat', 'a orange cat'], ['data/image/image01.jpg', 'data/image/image02.jpg']).

__Example SEARCH IMAGE Syntax__

You can find examples of using the algorithm query syntax in [search by text](/en/tutorials/thanosql_search/image_search/search_image_by_text/).

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS
SELECT *
FROM unsplash_data
```
