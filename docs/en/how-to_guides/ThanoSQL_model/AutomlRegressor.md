---
title: AutomlRegressor
---

# __AutomlRegressor__

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
USING AutomlRegressor
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS 절**

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

The "**OPTIONS**" clause allows you to change the value of the AutomlRegressor parameter from its default value. The meaning of each parameter is as follows.

- "target" : Sets the column in the data table to target the regression prediction model.
- "impute_type" : Sets how the data table handles empty values. (DEFAULT: "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : applies an algorithm that predicts through the remaining properties for empty values.
- "features_to_drop" : Sets columns not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how the data table handles outliers. (DEFAULT: "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, we use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (It works efficiently on a data set with many variables)  
> "knn" : A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : means the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**BUILD MODEL Query Syntax Example**

An example of using the algorithm query syntax can be found in [Create a prediction model using Auto-ML](/en/tutorials/thanosql_ml/regression/automl_regression/).

```sql
%%thanosql
BUILD MODEL bike_regression
USING AutomlRegressor
OPTIONS (
    target='count',
    impute_type='simple',
    datetime_attribs=['datetime'],
    overwrite = True
    )
AS
SELECT *
FROM bike_sharing
```

## **FIT MODEL Query Syntax**

This "**FIT MODEL**" query syntax lets you re-learn artificial intelligence models. The "**FIT MODEL**" expression allows you to re-learn defined data sets via query_expr after "**AS**".

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

**OPTIONS 절**

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

The "**OPTIONS**" clause allows you to change the value of the AutomlRegressor parameter from its default value. The meaning of each parameter is as follows.

- "target" : Sets the column in the data table to target the regression prediction model.
- "impute_type" : Sets how the data table handles empty values. (DEFAULT: "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : applies an algorithm that predicts through the remaining properties for empty values.  
- "features_to_drop" : Sets columns not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how the data table handles outliers. (DEFAULT: "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, we use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (It works efficiently on a data set with many variables)  
> "knn" : A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : means the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**FIT MODEL Query Syntax Example**

An example of using the algorithm query syntax can be found in [Re-learning the model](/en/how-to_guides/ThanoSQL_ml/FIT_MODEL_SYNTAX/)

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True

    )
AS
SELECT *
FROM titanic_train
```

## **TRANSFORM USING Query Syntax**

This "**TRANSFORM MODEL**" query syntax can be used to apply the same preprocessing method used to create AI models to test datasets. The "**TRANSFORM MODEL**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

TRANSFORM USING (model_name_expression)
AS
(query_expr)
```

**TRANSFORM USING Query Syntax example**

An example of using the algorithm query syntax can be found in [Preprocessing data for model application](/en/how-to_guides/ThanoSQL_ml/TRANSFORM_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```

## **PREDICT USING Query Syntax**

Use this "**PREDICT USING**" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more.
The "**PREDICT USING**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
AS
(query_expr)
```

**Example PREDICT USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create a prediction model using Auto-ML](/en/tutorials/thanosql_ml/regression/automl_regression/).

```sql
%%thanosql
PREDICT USING bike_regression
AS
SELECT *
FROM bike_sharing_test
```

## **EVALUATE USING Query Syntax**

You can use this "**EVALUATE USING**" query syntax to perform evaluation on the AI model. The "**EVALUATE USING**" expression evaluates the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression
    )
AS
(query_expr)
```

**OPTIONS 절**

```sql
OPTIONS(
    target = column_name
    )
```

The "**OPTIONS**" clause allows you to change the value of the parameters in the AutomlRegressor from their default values. The meaning of each parameter is as follows.

- "target" : In the data table, set the columns that target the classification prediction model.

**Example EVALUATE USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create a prediction model using Auto-ML](/en/tutorials/thanosql_ml/regression/automl_regression/).

```sql
%%thanosql
EVALUATE USING bike_regression
OPTIONS (
    target='count'
    )
AS
SELECT *
FROM bike_sharing
```
