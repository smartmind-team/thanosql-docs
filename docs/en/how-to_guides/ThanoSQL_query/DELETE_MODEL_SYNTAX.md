---
title: DELETE  MODEL
---

# __DELETE MODEL__

## __1. DELETE MODEL Syntax Overview__

The "__DELETE MODEL__" syntax allows the user to delete models created in the database.

## __2. DELETE MODEL Syntax__

```sql
%%thanosql
DELETE MODEL [Model_name_to_delete]
```

## __3. DELETE MODEL Syntax example__

The example below uses the syntax "__DELETE MODEL__" to delete the <mark style="background-color:#E9D7FD ">titanic_classification</mark> classification model from the database that you created in [BUILD MODEL](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
DELETE MODEL titanic_classification
```
