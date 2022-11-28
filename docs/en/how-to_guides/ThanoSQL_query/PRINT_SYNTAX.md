---
title: PRINT
---

# __PRINT__

## __1. PRINT Statement__
The "__PRINT__" statement allows users to to output images, audio, and video files.

## __2. PRINT Syntax__

The "__PRINT__" syntax
```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
AS
[dataset_to_output]
```

The "__PRINT__" syntax with an "__OPTIONS__" clause.

```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
OPTIONS (
    image_col | audio_col | video_col = [image_path_column_name]
    )
AS
[dataset_to_output]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "image_col | audio_col | video_col":The name of a column to be printed. If the column name is not specified, "image_path" | "audio_path" | "video_path" is the default value and if such column does not exist in the table an error message will be shown.

## __3. PRINT Example__

### __3.1 Image Print__

Outputs image files from the table.

```sql
%%thanosql
PRINT IMAGE
OPTIONS (
    image_col='image'
    )
AS
SELECT *
FROM junyoung_img
```

!!! note ""
    - `junyoung_img` : table containing paths of the image files

### __3.2 Audio Print__

Outputs audio files from the table.

```sql
%%thanosql
PRINT AUDIO
OPTIONS (
    audio_col='audio'
    )
AS
SELECT *
FROM junyoung_aud
```

[![IMAGE](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)

!!! note ""
    - `junyoung_aud` : table containing paths of the audio files

### __3.3 Video Print__

Outputs video files from the table.

```sql
%%thanosql
PRINT VIDEO
OPTIONS (
    video_col='video'
    )
AS
SELECT *
FROM junyoung_vid
```

!!! note ""
    - `junyoung_vid` : table containing paths of the video files

### __3.4 Print with a subquery__

The following statement outputs the results of "__SEARCH__" statement created in the nested [SEARCH](/en/how-to_guides/ThanoSQL_query/SEARCH_SYNTAX). 

```sql
%%thanosql
PRINT IMAGE AS(
    SELECT image_path, score
    FROM (
        SEARCH IMAGE text='12345'
        USING mymodel
        AS
        SELECT *
        FROM mnist_dataset)
    ORDER BY score DESC
    LIMIT 10
    )
```