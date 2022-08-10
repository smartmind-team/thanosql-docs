---
title: Check the saved model
---

# **Check the saved model (LIST)**

## Preface

- Last Modified Date : {{ git_revision_date_localized }}

## **1. LIST Syntax Overview**

The "**LIST**" syntax allows the user to view the pre-built models ("THANOSQL MODEL") of current ThanoSQL and the models "MODEL" created by the user.

## **2. LIST Syntax**

The "**LIST MODEL**" syntax checks the list of models you have created.

!!! Failure "Caution"
However, if there is no saved model, an error occurs.

```sql
%%thanosql
LIST MODEL
```

<a href = "/img/thanosql_syntax/query/LIST/img1.png">
    <img src = "/img/thanosql_syntax/query/LIST/img1.png"> </img>
</a>

The syntax "**LIST THANOSQL MODEL**" checks the list of pre-built models in ThanoSQL.

```sql
%%thanosql
LIST THANOSQL MODEL
```

<a href = "/img/thanosql_syntax/query/LIST/img2.png">
    <img src = "/img/thanosql_syntax/query/LIST/img2.png"> </img>
</a>

The syntax "**LIST THANOSQL TUTORIAL**" checks the list of Tutorials stored in ThanoSQL.

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```

<a href = "/img/thanosql_syntax/query/LIST/img3.png">
    <img src = "/img/thanosql_syntax/query/LIST/img3.png"> </img>
</a>

The syntax "**LIST THANOSQL DATASET**" checks the list of Datasets in ThanoSQL.

```sql
%%thanosql
LIST THANOSQL DATASET
```

<a href = "/img/thanosql_syntax/query/LIST/img4.png">
    <img src = "/img/thanosql_syntax/query/LIST/img4.png"> </img>
</a>

The "**LIST TABLE**" syntax checks the list of tables you created.

!!! Failure "Caution"
However, if the created table does not exist, an error occurs.

```sql
%%thanosql
LIST TABLE
```

<a href = "/img/thanosql_syntax/query/LIST/img5.png">
    <img src = "/img/thanosql_syntax/query/LIST/img5.png"> </img>
</a>
