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
- VALUE means just value.


!!! note ""
    __literal__ : A fixed or unchangeable value, also known as Constant.
    > Each literal has a special data type such as column, in the data table.


## __BUILD MODEL Query Syntax__

Use this "__BUILD MODEL__" query syntax to develop an AI model.
The "__BUILD MODEL__" expression allows you to learn defined data sets through query_expr after "__AS__".

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
    Use this query to save the base artificial intelligence model that follows USING as the model_name_expression name.

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

The "__OPTIONS__" clause allows you to change the value of the parameters in the AutomlClassifier from their default values. The meaning of each parameter is as follows.

- "target" : Set the column that is the target value for the classification prediction model in the data table.
- "impute_type" : Sets how empty values are handled in the data table.(DEFAULT : "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : For empty values, process by applying an algorithm that predicts through the remaining properties.
- "features_to_drop" : Sets the columns that are not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how outliers are handled in the data table.(DEFAULT : "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (It works efficiently on data sets with many variables.)  
>  "knn" : A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : Indicates the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" :Sets the presence or absence of an overwriteable model with the same name if it exists. If true, the existing model will be replaced with the new model. (DEFAULT : False)

__BUILD MODEL Query Syntax example__

You can find examples of using the algorithm query syntax in the [Creating a classification model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

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

## __FIT MODEL Query Syntax__

This "__FIT MODEL__" query syntax lets you re-learn artificial intelligence models. The "__FIT MODEL__" expression allows you to re-learn defined data sets via query_expr after "__AS__".

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

The "__OPTIONS__" clause allows you to change the value of the parameters in the AutomlClassifier from the default value. The meaning of each parameter is as follows.

- "target" : In the data table, set the columns that target the classification prediction model.
- "impute_type" : Sets how the data table handles empty values.
  (DEFAULT : "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : For empty values, process by applying an algorithm that predicts through the remaining properties.
- "features_to_drop" : Sets the columns that are not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how outliers are handled in the data table. (DEFAULT : "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, Isolation Forest is used to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples (even on a data set with many variables)  
> "knn" :A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : Indicates the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Sets the presence or absence of an overwriteable model with the same name if it exists. If true, the existing model will be replaced with the new model. (DEFAULT : False)

__FIT MODEL Query Syntax example__

You can find examples of using the algorithm query syntax in [Retrain the model](/en/how-to_guides/ThanoSQL_ml/FIT_MODEL_SYNTAX/)

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

## __TRANSFORM USING Query Syntax__

This "__TRANSFORM MODEL__" query syntax can be used to apply the same preprocessing method used to create AI models to test datasets. The "__TRANSFORM MODEL__" expression can preprocess the dataset defined by query_expr after "__AS__".

```sql
query_statement:
    query_expr

TRANSFORM USING (model_name_expression)
AS
(query_expr)
```

__TRANSFORM USING Query Syntax eaxmple__

An example of using the algorithm query syntax can be found in [Preprocessing data for model application](/en/how-to_guides/ThanoSQL_ml/TRANSFORM_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```

## __PREDICT USING Query Syntax__

Use this "__PREDICT USING__" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "__PREDICT USING__" expression can preprocess the dataset defined by query_expr after "__AS__".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
AS
(query_expr)
```

__PREDICT USING Query Syntax example__

You can find examples of using the algorithm query syntax in [Create Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
PREDICT USING titanic_classification
AS
SELECT *
FROM titanic_test
```

## __EVALUATE USING Query Syntax__

You can use this "__EVALUATE USING__" query syntax to perform evaluation on the AI model. The "__EVALUATE USING__" expression evaluates the dataset defined by query_expr after "__AS__".

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

The "__OPTIONS__" clause allows you to change the value of the parameters in the AutomlClassifier from their default values. The meaning of each parameter is as follows.

- "target" : In the data table, set the column that is the target value for the classification prediction model.

__EVALUATE USING Query Syntax example__

You can find examples of using the algorithm query syntax in [Create Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

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