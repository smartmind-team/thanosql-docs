---
title: Whisper
---

# __Whisper__

__Notation Conventions__

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces `{}` are used to bind combinations of options.
- The bracket `[]` indicates an optional clause.
- An ellipsis following a comma in brackets [,...] means that the preceding item can be repeated as a comma-separated list
- The vertical bar `|` represents the logic `OR`.
- VALUE represents a regular value.

!!! note ""
    - __literal__: a fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.

## __PREDICT Syntax__

Use the "__PREDICT__" statement to apply AI models to perform prediction, classification, recommendation, and more. The "__PREDICT__" statement can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

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

__OPTIONS Clause__

```sql
OPTIONS(
    (audio_col=column_name),
    [batch_size=VALUE],
    (language=expression),
    (task={'transcribe'|'translate'}),
    [result_col=column_name],
    [table_name=expression]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "audio_col": the name of the column containing the audio path to be used for prediction (str, default: 'audio_path')
- "batch_size": the size of the dataset bundle utilized in a single cycle of prediction (int, optional, default: 16)
- "language": specifies the language of the audio file. If selected as ‘auto’, the model will recognize the language from the available pool of 99 languages (str, default: 'auto')
- "task": type of work to do (str, 'transcribe'|'translate', default: 'transcribe')
>  "transcribe": the task of recognizing speech 
>  "translate": the task of translating recognized speech back into English. This process is different from the ordinary translating task in that it translates Korean speech directly into English text, skipping the middle task of translating to Korean text
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column (str, optional)


- `task=‘transcribe’` 옵션을 지정하면 음성 인식을 합니다.
- `task=‘translate’` 옵션을 지정하면 인식된 음성을 영어로 출력합니다. 이 과정은 ‘한국어 음성‘을 바로 ‘영어 텍스트‘로 번역하는 것으로, 중간에 ‘한국어 텍스트’를 거치지 않는 다는 점이 일반적인 번역 태스크와 다릅니다.

__PREDICT Example__

An example "__PREDICT__" query can be found in [Using a Speech Recognition Model](/en/tutorials/thanosql_ml/audio_recognition/speech_recognition2/).

```sql
%%thanosql
PREDICT USING tutorial_whisper_small
OPTIONS (
    audio_col='audio_path',
    language='auto',
    task='transcribe',
    result_col='predict_result',
    table_name='korean_voice'
    )
AS
SELECT *
FROM korean_voice
```

## __EVALUATE Syntax__

Use the "__EVALUATE__" statement to evaluate the AI model. The "__EVALUATE__" expression evaluates a model using the dataset defined by the query_expr that comes after the "__AS__" clause.

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
    (audio_col=column_name),
    [batch_size=VALUE],
    (language=expression),
    (task={'transcribe'|'translate'}),
    (text_col=column_name)
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "audio_col": the name of the column containing the audio path to be used for evaluation (str, default: 'audio_path')
- "batch_size" is the size of dataset bundle utilized in a single cycle of evaluation (int, optional, default: 16)
- "language": specifies the language of the audio file. If selected as ‘auto’, the model will recognize the language from the available pool of 99 languages (str, default: 'auto')
- "task": type of work to do (str, 'transcribe'|'translate', default: 'transcribe')
>  "transcribe": the task of recognizing speech 
>  "translate": the task of translating recognized speech back into English. This process is different from the ordinary translating task in that it translates Korean speech directly into English text, skipping the middle task of translating to Korean text
- "text_col": the name of the column containing information about the target (str, default: 'text')