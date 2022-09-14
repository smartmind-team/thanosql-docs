---
title: DELETE  MODEL
---

# __DELETE MODEL__

## __1. Overview__

The "__DELETE MODEL__" statement allows users to delete built models.

## __2. Syntax__

```sql
%%thanosql
DELETE MODEL [model_name_to_delete]
```

## __3. Example__

The example below demonstrates a method to use a "__DELETE MODEL__" statement to delete the <mark style="background-color:#E9D7FD ">titanic_classification</mark> model built with the [BUILD MODEL](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
DELETE MODEL titanic_classification
```

