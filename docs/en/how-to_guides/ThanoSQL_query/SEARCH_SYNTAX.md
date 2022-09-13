---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH Syntax Overview__

The "__SEARCH__" query syntax searches for content, meaning, or similarity in unstructured data.

## __2. SEARCH Syntax__

```sql
%%thanosql

SEARCH [custom_data_table_name]
USING [AI_model_to_use]
AS [data_set_to_use]
```

## __3. SEARCH Query Syntax example__

The query below uses `Color_Descriptor`, an AI model for quantifying images, to search for similar images.

```sql
%%thanosql
SEARCH IMAGE images='tutorial/image_search/images/20150617_132435.jpg'
USING Color_Descriptor
AS
SELECT *
FROM color_descriptor_table_test
```

[![IMAGE](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)
