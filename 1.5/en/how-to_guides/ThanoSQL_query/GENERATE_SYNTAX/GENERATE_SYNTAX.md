---
title: GENERATE
---

# __GENERATE__

## __1. GENERATE Statement__

The "__GENERATE__" statement allows users to apply generative models to perform FAQ generation, keyword extraction, service evaluation, sentiment analysis, text summarization, and more.


## __2. GENERATE Syntax__

```sql
query_statement:
    query_expr

GENERATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

## __3. GENERATE Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. 
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.

```sql
%%thanosql
GENERATE USING GPT
OPTIONS (
    task = 'faq', 
    text_col='text_data',
    result_col='faq_result'
    )
AS
SELECT *
FROM voc_text_data
```

!!! note
    - To use GPT for the GENERATE function, you will need an OpenAI API token. If you possess a token or require one, please reach out to us at [ThanoSQL Contact](contact@smartmind.team).