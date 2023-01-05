---
title: {function statement name}
---

# __{function statement name}__

## __1. {function statement name} Statement__

The "__{function statement name}__" statement {explanation}.

## __2. {function statement name} Functions__

<!-- In most cases, there will be more than one function within each Function Statement modules. -->

### __2-1. {sub-function name}__

The "__{sub-function name}__" is a function that {explanation}.

__{sub-function name} Syntax__

```sql
{function statement query syntax
e.g. 
FUNCTION {function statement name}.{sub-function name}
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} } 
}
```

__OPTIONS Clause__

```sql
{option of the function explanation
e.g. 
OPTIONS (
    (label_col=column_name),
    (emb_col=column_name),
    [result_col=expression]
    )
}
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

{definition of all the parameters used by the syntax
e.g.
- "label_col": the name of the column containing information about the label (str)
- "emb_col": the name of the column containing information about the embedding (str)
- "result_col": the name of the column containing the embeddings' mean values (str, optional, default: 'mean_emb_result')
}

__{sub-function name} Example__

```sql
%%thanosql
{e.g.
FUNCTION compute.groupby_mean
OPTIONS (
    label_col='label',
    emb_col='convert_result',
    result_col='result_col'
    )
AS
SELECT * FROM mnist_train_test
}
```

### __2-2. {another sub-function if exists}__

<!-- format should be the exactly same as the 2-1 and this should be done for all the sub-functions for each modules -->