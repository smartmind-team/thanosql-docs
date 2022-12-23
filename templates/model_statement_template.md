---
title: {model name}
---

# __{model name}__

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

## __{syntax name} Syntax__

The "__{syntax name}__" statement {brief explanation of the syntax}

```sql
{syntax query explanation
e.g. 
query_statement:
query_expr

CONVERT USING (model_name_expression)
OPTIONS (
expression [ , ...]
    )
AS
(query_expr)
}
```

__OPTIONS Clause__

```sql
{option of the syntax explanation
e.g. 
OPTIONS(
    (table_name=expression),
    (image_col=column_name),
    [batch_size=VALUE]
)
}
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

{definition of all the parameters used by the syntax
e.g.
- "table_name": the name of the new table to be created (str)
- "image_col": the name of the column containing the image path. (str, default: 'image_path')
- "batch_size": the size of the dataset bundle read during a single train. (int, optional, default: 16)
}

__{syntax name} Example__

An example "__{syntax name}__" query can be found in [{name of the tutorial using this syntax}]({link to a tutorial}).

```sql
%%thanosql
{syntax query example
e.g.
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col='image_path',
    table_name='unsplash_data',
    batch_size=128
    )
AS
SELECT *
FROM unsplash_data
}
```

{more syntaxes and examlpes as needed}



<!-- BELOW ARE EXAMPLES OF MODEL SYNTAX AND CORRESPONDING OPTIONS -->
<!-- =================================== -->
<!-- MODEL SYNTAX Examples -->
<!-- BUILD -->
## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" statement to develop an AI model. The "__BUILD MODEL__" statement allows you to train a model using datasets defined with the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```


<!-- FIT -->
## __FIT MODEL Syntax__

Use the "__FIT MODEL__" statement to retrain an AI models. The "__FIT MODEL__" statement allows you to retrain a model using datasets defined with the query_expr that comes after the "__AS__" clause. In this case, the label of the data used for retraining should be the same as the label used for the previous training.

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


<!-- PREDICT -->
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


<!-- EVALUATE -->
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


<!-- CONVERT -->
## __CONVERT Syntax__

Use the "__CONVERT__" statement to convert data into the vectors and add it to the table.

```sql
query_statement:
    query_expr

CONVERT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```


<!-- SEARCH -->
## __SEARCH Syntax__

Use the "__SEARCH__" statement to retrieve the desired data (image|audio|video|text|keyword).

```sql
query_statement:
    query_expr

SEARCH IMAGE 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```



<!-- =================================== -->
<!-- OPTIONS Examples -->
__OPTIONS Clause__
<!-- column_name, VALUE, {}, expression -->
```sql
OPTIONS(
    (text_col=column_name),
    (label_col=column_name),
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [overwrite={True|False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

1  
<!-- Albert & Electra -->
<!-- BUILD & FIT OPTIONS -->
- "text_col": the name of the column containing the text to be used for the training (str, default: 'text')
- "label_col": the name of the column containing information about the target (str, default: 'label')
- "batch_size" is the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 3)
- "learning_rate": the learning rate of the model (float, optional, default: 1e-4) 
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "text_col": the column containing the text to be used for prediction (str, default: 'text')
- "batch_size" is the size of dataset bundle utilized in a single cycle of prediction (int, optional, default: 16)
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)

<!-- EVALUATE OPTIONS -->
- "text_col": the column containing the text to be used for evaluation (str, default: 'text')
- "label_col": the name of the column containing information about the target (str, default: 'label')
- "batch_size" is the size of dataset bundle utilized in a single cycle of evaluation (int, optional, default: 16)


2
<!-- Wav2Vec2 -->
<!-- BUILD & FIT OPTIONS -->
- "audio_col": the name of the column containing the audio path to be used for training (str, default: 'audio_path')
- "text_col": the name of the column containing the audio script information (str, default: 'text')
- "max_epochs": number of times to train with the training dataset (int, optional, default: 5)
-  "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "learning_rate": the learning rate of the model (float, optional, default: 1e-4) 
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False) 

<!-- PREDICT OPTIONS -->
- "audio_col": the name of the column containing the audio path to be used for prediction (str, default: 'audio_path')
- "batch_size" is the size of dataset bundle utilized in a single cycle of prediction (int, optional, default: 16)
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)

<!-- EVALUATE OPTIONS -->
- "audio_col": the column containing the audio path to be used for evaluation (str, default: 'audio_path')
- "text_col": the name of the column containing information about the target (str, default: 'text')
- "batch_size" is the size of dataset bundle utilized in a single cycle of evaluation (int, optional, default: 16)


3
<!-- AutoML -->
<!-- BUILD & FIT OPTIONS -->
- "target_col": the name of the column containing the target value of the classification model (str, default: 'target') 
- "impute_type": determines how empty values ​​(NaNs) are handled (str, optional, 'simple'|'iterative' , default: 'simple') 
- "features_to_drop": selects columns that cannot be used for training (int, optional, default: 300)
- "time_left_for_this_task": the total time given to find a suitable classification model in seconds (int, optional, default: 300)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)

<!-- EVALUATE OPTIONS -->
- "target_col": the name of the column containing the target value of the classification model (str, default: 'target') 


4
<!-- ConvNext, EfficientNet -->
<!-- BUILD & FIT OPTIONS -->
- "image_col": name of column containing the image path (str, default: 'image_path')
- "label_col": name of the column containing information about the target value (str, default: 'label')
- "max_epochs": number of times to train with the training dataset (int, optional, default: 3)
- "learning_rate": the learning rate of the model (float, optional, default: 1e-3) 
- "input_size": size of the image to be used for training (int, optional)
- "color": color of the image to be used for training (str, optional, 'RGB'|'GRAY', default: 'RGB')
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "image_col": the name of the column where the path of the image used for prediction is stored (str, default: 'image_path')
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "batch_size": the size of the dataset bundle utilized in a single cycle of prediction (int, optional, default: 16)
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)
- "input_size": size of the image to be used for prediction (int, optional)

<!-- EVALUATE OPTIONS -->
- "image_col": the column containing the image path to be used for evaluation (str, default: 'image_path')
- "label_col": the name of the column containing information about the target (str, default: 'label')
- "batch_size" is the size of dataset bundle utilized in a single cycle of evaluation (int, optional, default: 16)
- "input_size": size of the image to be used for evaluation (int, optional)


5
<!-- SimCLR -->
<!-- BUILD & FIT OPTIONS -->
- "image_col": the name of the column containing the image path (str, default: 'image_path')
- "max_epochs": number of times to train with the training dataset (int, optional, default: 5)
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 256)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

<!-- CONVERT OPTIONS -->
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)
- "image_col": the name of the column containing the image path (str, default: 'image_path')
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 256)

<!-- SEARCH IMAGE BY IMAGE -->
- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional, default: 'search_result')


6. 
<!-- Whisper -->
<!-- PREDICT OPTIONS -->
- "audio_col": the name of the column containing the audio path to be used for prediction (str, default: 'audio_path')
- "batch_size": the size of the dataset bundle utilized in a single cycle of prediction (int, optional, default: 16)
- "language": specifies the language of the audio file. If selected as ‘auto’, the model will recognize the language from the available pool of 99 languages (str, default: 'auto')
- "task": type of work to do (str, 'transcribe'|'translate', default: 'transcribe')
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)

<!-- EVALUATE OPTIONS -->
- "audio_col": the name of the column containing the audio path to be used for evaluation (str, default: 'audio_path')
- "batch_size" is the size of dataset bundle utilized in a single cycle of evaluation (int, optional, default: 16)
- "language": specifies the language of the audio file. If selected as ‘auto’, the model will recognize the language from the available pool of 99 languages (str, default: 'auto')
- "task": type of work to do (str, 'transcribe'|'translate', default: 'transcribe')
- "text_col": the name of the column containing information about the target (str, default: 'text')


7.
<!-- ViLT -->
<!-- PREDICT OPTIONS -->
- "image_col": the name of the column where the path of the image used for prediction is stored (str, default: "image_path")
- "question": the question text to be used for prediction (str)
- "result_col": defines the name of the column to contain the result (str, optional, default: "predict_result")
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)


8. 
<!-- CLIP -->
<!-- CONVERT OPTIONS -->
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)
- "image_col": the name of the column containing the image path (str, default: 'image_path')
- "text_col": the name of the column containing the text (str, default: 'text')
- "convert_type": file type for vectorization (str, 'image'|'text', default: 'image')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16) 
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')

<!-- SEARCH IMAGE BY TEXT -->
- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional, default: 'search_result')


9. 
<!-- XCLIP -->
<!-- CONVERT OPTIONS -->
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)
- "video_col": the name of the column containing the video path (str, default: 'video_path')
- "text_col": the name of the column containing the text (str, default: 'text')
- "convert_type": file type for vectorization (str, 'image'|'text', default: 'image')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16) 
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')

<!-- SEARCH VIDEO BY TEXT -->
- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional, default: 'search_result')


10. 
<!-- SBERT -->
<!-- BUILD & FIT OPTIONS -->
- "text_col": a column containing movie review data in the data table (str, default: 'text')
- "max_epochs": number of times to train with the training dataset (int, optional, default: 1)
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16) 
- "learning_rate": the learning rate of the model (float, optional, default: 3e-5) 
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False) 

<!-- CONVERT OPTIONS -->
- "text_col": a column containing movie review data in the data table (str, default: 'text')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')

<!-- SEARCH TEXT BY TEXT -->
- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional. default: 'search_result')

<!-- SEARCH KEYWORD -->
- "lang": language to use (str, optional, 'ko'|'en' default: 'ko')
- "text_col": a column containing movie review data in the data table (str, default: 'text')
- "ngram_range": minimum and maximum number of words for each keyword ex) [1, 3]. In most situations, keywords are extracted according to the maximum number of words (list[int, int], optional, default: [1, 2])
- "top_n": number of keywords to be extracted, in order of highest similarity (int, optional, default: 5)
- "diversity": variety of keywords to be extracted. The higher the value, the more diverse the keywords will be 0 <= diversity <= 1 (float, optional, default: 0.5)
- "use_stopwords": whether to exclude words that do not have a significant meaning (bool, optional, True|False, default: True)
- "threshold": minimum value of similarity value of keywords to be extracted (float, optional, default: 0.0)