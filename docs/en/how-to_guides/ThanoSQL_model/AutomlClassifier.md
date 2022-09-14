---
title: AutomlClassifier
---

# __AutomlClassifier__

__Notation Conventions__

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces `{}` are used to bind combinations of options.
- The bracket `[]` indicates an optional clause.
- An ellipsis following a comma in brackets [,...] means that the preceding item can be repeated as a comma-separated list
- The vertical bar `|` represents the logic `OR`.
- VALUE represents a regular value.


!!! note ""
    __literal__ : A fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.



## __BUILD MODEL Query Statement__

Use the "__BUILD MODEL__" query statement to develop an AI model.
The "__BUILD MODEL__" statement allows you to train datasets defined with the query_expr that comes after the "__AS__" clause.

``` sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression) 
USING AutomlClassifier
OPTIONS (
    expression [ , ...]
    )
AS 
(query_expr)
``` 

!!! faq ""
    Use this query to save the base model that follows the USING clause as model_name_expression.

 __OPTIONS clause__

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the AutomlClassifier model. The definition of each parameter is as follows.

- "target" : Sets the name of the column that has the target value for the classification prediction model in the table.
- "impute_type" : Determines how empty values are handled in the data table.(DEFAULT : "simple")
> "simple" : For empty values, categorical variables are treated as the most common value and continuous variables are treated as the mean.  
> "iterative" : Applies an algorithm that predicts empty values with the remaining properties.
- "features_to_drop" : Selects columns that are not needed for training.
- "datetime_attribs" : Selects columns corresponding to the date.
- "outlier_method" : Determines how outliers are handled in the table.(DEFAULT : "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using the Principal Component Analysis (PCA).  
> "iso" : Use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (Works efficiently on datasets with many variables.)  
>  "knn" : Use a K-NN-based approach to detect abnormal samples based on the distance between each data.
- "time_left_for_this_task" : Indicates the time the classifier will take to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (DEFAULT : False)

__BUILD MODEL Query Statement example__

Examples can be found in [Creating a classification model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
BUILD MODEL titanic_classification
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    outlier_method = 'pca',
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

## __FIT MODEL Query Statement__

The "__FIT MODEL__" query statement lets you re-train artificial intelligence models. The "__FIT MODEL__" expression allows you to re-train datasets defined by the query_expr that comes after the "__AS__" clause.

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

__OPTIONS clause__

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]

    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the AutomlClassifier model. The definition of each parameter is as follows.

- "target" : Sets the name of the column that has the target value for the classification prediction model in the table.
- "impute_type" : Determines how empty values are handled in the data table.(DEFAULT : "simple")
> "simple" : For empty values, categorical variables are treated as the most common value and continuous variables are treated as the mean.  
> "iterative" : Applies an algorithm that predicts empty values with the remaining properties.
- "features_to_drop" : Selects columns that are not needed for training.
- "datetime_attribs" : Selects columns corresponding to the date.
- "outlier_method" : Determines how outliers are handled in the table.(DEFAULT : "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using the Principal Component Analysis (PCA).  
> "iso" : Use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (Works efficiently on datasets with many variables.)  
>  "knn" : Use a K-NN-based approach to detect abnormal samples based on the distance between each data.
- "time_left_for_this_task" : Indicates the time the classifier will take to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (DEFAULT : False)

__FIT MODEL Query Statement example__

Examples can be found in [Retrain the model](/en/how-to_guides/ThanoSQL_ml/FIT_MODEL_SYNTAX/)

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite=True
    )
AS
SELECT *
FROM titanic_train
```

## __TRANSFORM USING Query Statement__

The "__TRANSFORM MODEL__" query statement is used to apply the same preprocessing method used to create AI models on your test datasets. The "__TRANSFORM MODEL__" expression can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

TRANSFORM USING (model_name_expression)
AS
(query_expr)
```

__TRANSFORM USING Query Statement example__

An example can be found in [Preprocessing data for model application](/en/how-to_guides/ThanoSQL_ml/TRANSFORM_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```

## __PREDICT USING Query Statement__

Use the "__PREDICT USING__" query statement to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "__PREDICT USING__" expression can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
AS
(query_expr)
```

__PREDICT USING Query Statement Example__

Examples can be found in [Create Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
PREDICT USING titanic_classification
AS
SELECT *
FROM titanic_test
```

## __EVALUATE USING Query Statement__

You can use the "__EVALUATE USING__" query statement to evaluate the AI model. The "__EVALUATE USING__" expression evaluates the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    target = expression
    )
AS
(query_expr)
```

__OPTIONS clause__

```sql
OPTIONS(
    target = column_name
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the AutomlClassifier model. The definition of each parameter is as follows.

- "target" : Sets the name of the column that has the target value for the classification prediction model in the table.

__EVALUATE USING Query Statement Example__

Examples can be found in [Create Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
EVALUATE USING titanic_classification
OPTIONS (
    target = 'survived'
    )
AS
SELECT *
FROM titanic_train
```