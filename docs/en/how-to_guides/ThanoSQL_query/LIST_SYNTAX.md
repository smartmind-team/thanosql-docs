---
title: LIST
---

# __LIST__

## Preface

- Updated Date : {{ git_revision_date_localized }}

## __1. LIST Syntax Overview__

The "__LIST__" syntax allows the user to view the pre-built models ("THANOSQL MODEL") of current ThanoSQL and the models ("MODEL") created by the user.

## __2. LIST Syntax__

The "__LIST MODEL__" syntax checks the list of models you have created.

!!! Failure "Caution"
    However, if there is no saved model, an error occurs.

```sql
%%thanosql
LIST MODEL
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img1.png)](/img/thanosql_syntax/query/LIST/img1.png)

The syntax "__LIST THANOSQL MODEL__" checks the list of pre-built models in ThanoSQL.

```sql
%%thanosql
LIST THANOSQL MODEL
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img2.png)](/img/thanosql_syntax/query/LIST/img2.png)

The syntax "__LIST THANOSQL TUTORIAL__" checks the list of Tutorials stored in ThanoSQL.

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img3.png)](/img/thanosql_syntax/query/LIST/img3.png)

The syntax "__LIST THANOSQL DATASET__" checks the list of Datasets in ThanoSQL.

```sql
%%thanosql
LIST THANOSQL DATASET
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img4.png)](/img/thanosql_syntax/query/LIST/img4.png)

The "__LIST TABLE__" syntax checks the list of tables you created.

!!! Failure "Caution"
    However, if the created table does not exist, an error occurs.

```sql
%%thanosql
LIST TABLE
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img5.png)](/img/thanosql_syntax/query/LIST/img5.png)
