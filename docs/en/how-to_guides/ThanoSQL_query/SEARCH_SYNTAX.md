---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH Statement__

The "__SEARCH__" statement allows users to search for content, meaning, or similarity from the unstructured data table.

## __2. SEARCH Syntax__

```sql
%%thanosql
SEARCH IMAGE | AUDIO | VIDEO [input_data_used_for_searching]
USING [AI_model_to_use]
OPTIONS ([option_values_​​required_for_search])
AS [dataset_to_use]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "table_name" : The name of the new table to be created.

## __3. SEARCH Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
USING mymodel
OPTIONS (
    table_name='search_result_table'
) 
AS 
SELECT * 
FROM mnist_test
```
