---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH Statement__

The "__SEARCH__" statement allows users to search for content, meaning, or similarity from the unstructured data table.

## __2. SEARCH Syntax__

```sql
%%thanosql
SEARCH IMAGE | AUDIO | VIDEO | TEXT | KEYWORD
USING [AI_model_to_use]
OPTIONS ([option_values_​​required_for_search])
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "search_input_type": defines the image|text|audio|video type to be used for the search.
        - "search_input": defines the input to be used for the search. 
        - "emb_col": the column that contains the vectorized results.
        - "column_name": defines the name of the column that contains the search results. (default: "search_result")

## __3. SEARCH Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

```sql
%%thanosql
SEARCH IMAGE 
USING mymodel
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

