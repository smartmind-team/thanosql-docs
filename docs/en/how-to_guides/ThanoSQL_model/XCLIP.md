---
title: XCLIP
---

# __XCLIP__

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

Use the "__CONVERT__" statement to convert data into the vectors and add it to the table.

```sql
query_statement:
    query_expr

CONVERT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS (
    (video_col=column_name),
    (text_col=column_name),
    (convert_type={'video'|'text'}),
    [batch_size=VALUE],
    [result_col=column_name]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "video_col": the name of the column containing the video path (str, default: 'video_path')
- "text_col": the name of the column containing the text (str, default: 'text')
- "convert_type": file type for vectorization (str, 'video'|'text', default: 'video')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16) 
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')


__CONVERT Example__

An example "__CONVERT__" query can be found in [Search Video by Text](../../tutorials/thanosql_search/search_video_by_text/).

```sql
%%thanosql
CONVERT USING tutorial_search_xclip
OPTIONS (
    video_col='video_path',
    result_col='convert_result'
    )
AS
SELECT *
FROM kinetics700
```

## __SEARCH VIDEO Syntax__

Use the "__SEARCH VIDEO__" statement to retrieve the desired video data.

```sql
query_statement:
    query_expr
    
SEARCH VIDEO 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS (
    (search_by={image|text|audio|video}),
    (search_input=expression),
    (emb_col=column_name),
    [result_col=expression],
    [top_k=VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional, default: 'search_result')
- "top_k": number of rows to return. If set as None, returns the entire data table (int, optional, default: 1000)

__SEARCH VIDEO Example__

An example "__SEARCH VIDEO__" query can be found in [Search Video by Text](../../tutorials/thanosql_search/search_video_by_text/).

```sql
%%thanosql
SEARCH VIDEO 
USING tutorial_search_xclip
OPTIONS (
    search_by='text',
    search_input='bench press',
    emb_col='convert_result',
    result_col='score',
    top_k=10
    )
AS 
SELECT * 
FROM kinetics700
```
