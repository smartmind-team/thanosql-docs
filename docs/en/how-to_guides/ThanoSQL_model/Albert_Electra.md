---
title: Albert & Electra
---

# __Albert & Electra__

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

## __BUILD MODEL Query Syntax__

Use this "__BUILD MODEL__" query syntax to develop an AI model.
The "__BUILD MODEL__" expression allows you to learn defined data sets through query_expr after "__AS__".

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {AlbertKo | AlbertEn | ElectraKo | ElectraEn}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (text_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")
- "label_col" : Sets the column containing the path of the label in the data table. (DEFAULT : "label")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

__BUILD MODEL Query Syntax Example__

An example of using the algorithm query syntax can be found in [Create a Text Classification Model](/en/tutorials/thanosql_ml/classification/classification_electra/).

```sql
%%thanosql
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
  text_col='review',
  label_col='sentiment',
  epochs=1,
  batch_size=4,
  overwrite = True
  )
AS
SELECT *
FROM movie_review_train
```

## __FIT MODEL Query Syntax__

This "__FIT MODEL__" query syntax lets you re-learn artificial intelligence models. The "__FIT MODEL__" expression allows you to re-learn defined data sets via query_expr after "__AS__". In this case, the label of the data used for re-learning should be the same as the label used for previous learning.

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (text_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")
- "label_col" : Sets the column containing the path of the label in the data table. (DEFAULT : "label")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

## __PREDICT USING Query Syntax__

Use this "__PREDICT USING__" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "__PREDICT USING__" expression can preprocess the dataset defined by query_expr after "__AS__".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__Example PREDICT USING Query Syntax__

An example of using the algorithm query syntax can be found in [Create Text Classification Model](/en/tutorials/thanosql_ml/classification/classification_electra/).

```sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test
```

 __OPTIONS Clause__

```sql
OPTIONS(
    (text_col = column_name),
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")

## __EVALUATE USING Query Syntax__

You can use this "__EVALUATE USING__" query syntax to perform evaluation on the AI model. The "__EVALUATE USING__" expression evaluates the dataset defined by query_expr after "__AS__".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (text_col = column_name),
    [batch_size = VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")

- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
