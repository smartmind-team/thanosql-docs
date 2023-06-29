---
title: FUNCTION
---

# __FUNCTION__

## __1. FUNCTION Statement__

The "__FUNCTION__" statement allows users to process data using ThanoSQL's functoins from the "__FUNCTION__" modules.

## __2. FUNCTION Syntax__

The "__FUNCTION__" syntax with an "__AS__" clause. 

```sql
query_statement:
    query_expr

FUNCTION (function_module_expression).(function_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

The "__FUNCTION__" syntax with an "__FROM__" clause. 

```sql
FUNCTION (function_module_expression).(function_name_expression)
OPTIONS (
    expression [ , ...]
    )
FROM (file_path_expression)
```


!!! note "ThanoSQL's Modules and Functions That Can Be Used with '__FUNCTION__ Statement'"
    - compute
        - groupby_emb_mean
        - similarity
    - preprocess
        - video_to_df     