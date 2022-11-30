---
title: SBERT
---

# __SBERT__

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

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { SBERTKo | SBERTEn }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

!!! faq ""
    - Use this query to save the base model that comes after the USING clause as model_name_expression.

__OPTIONS Clause__

```sql
OPTIONS(
    (text_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [train = {True | False}]
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in a text model. The definition of each parameter is as follows.

- "text_col": the name of the column containing movie review data in the data table.
- "batch_size": the size of the dataset bundle read during a single train. (default: 16)
- "epochs": sets how many times the dataset is trained in total. (default: 1)
- "learning_rate": the learning rate of the model. (default: 3e-5)
- "train": determines whether to train the pretrained model. If False, the pretrained model is used. (default: True)
- "overwrite": overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (default: False)

__BUILD MODEL Example__

A sample BUILD MODEL query can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
%%thanosql
BUILD MODEL movie_text_search_model
USING SBERTEn
OPTIONS (
    text_col="review",
    overwrite=True
)
AS
SELECT *
FROM movie_review_train
```

## __CONVERT Syntax__

The "__CONVERT__" statement converts text data from an existing table into a vector and adds it to the table.

```sql
CONVERT USING (model_name_expression)
OPTIONS(
    (table_name = expression),
    (text_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (table_name=expression),
    (text_col=column_name),
    [batch_size=VALUE]
)
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "table_name": the name of the new table to be created.
- "text_col": the name of the column containing the movie review data.
- "batch_size": the size of the dataset bundle read during a single train. (default: 16)

__CONVERT Example__

Examples of CONVERT queries can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
%%thanosql
CONVERT USING movie_text_search_model
OPTIONS (
    text_col="review",
    table_name="movie_review_test",
    batch_size=32
    )
AS 
SELECT *
FROM movie_review_test
```

## __SEARCH Syntax__

You can use the "__SEARCH__" statement to retrieve the desired document from the table that generated the vectors.

```sql
SEARCH TEXT 
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


You can use the "__SEARCH__" statement to extract the keywords from the table that generated the vectors.

```sql
%%thanosql
SEARCH KEYWORD
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
    (text_col = column_name),
    [ngram_range = [int, int]],
    [use_stopwords = {True | False}]
    [lang = {en | ko}]
    [top_n = VALUE]
    [diversity = VALUE]
    [threshold = VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "text_col": the name of the column containing the movie review data.
- "ngram_range": minimum and maximum size of words for each keyword. (list[int, int], default: [1, 2])
- "use_stopwords": whether to exclude words that do not have a significant meaning.  (default: True) 
- "lang": en, ko
- "top_n": the number of keywords to be extracted, in order of highest similarity. (int, default: 5)
- "diversity": the variety of keywords to be extracted. The higher the value, the more diverse the keywords will be. 0 <= diversity <= 1 (float, default: 0.5)
- "threshold": the minimum value of similarity value of keywords to be extracted. (float, default: 0.0)

__SEARCH Example__

Examples of SEARCH queries can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
%%thanosql
SEARCH KEYWORD
USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    ngram_range=[1, 3],
    use_stopwords=True
    )
AS (
    SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
    FROM (
        SEARCH TEXT 
        USING nsmc_text_search_model
        OPTIONS (
            search_input_type="text",
            search_input="가볍게 볼 수 있는 코미디 영화",
            emb_col="convert_result",
            column_name="search_result"
        )
        AS 
        SELECT * 
        FROM nsmc_test
        )
    ORDER BY score DESC 
    LIMIT 10
)
```