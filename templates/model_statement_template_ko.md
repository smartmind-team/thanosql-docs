---
title: {모델 이름}
---

# __{모델 이름}__

__표기법 규칙__

- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.
- 중괄호 {}는 옵션 조합을 묶는 데 사용됩니다.
- 대괄호 `[]`는 선택적 절을 나타냅니다.
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된 
목록으로 반복될 수 있음을 의미합니다.
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.
- VALUE는 값을 의미합니다.

!!! note ""
    - __리터럴__: 고정되거나 변경할 수 없는 값을 의미하며 상수(Constant)라고도 불립니다.
    > 각 리터럴은 테이블에서 컬럼과 같은 특별한 자료형을 가지고 있습니다.

## __{구문 이름} 구문__

"__{구문 이름}__" 구문을 사용하여 {구문 설명}

예시)
"__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. "__BUILD MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 학습할 수 있습니다.

```sql
{구문 쿼리 설명
예시)
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
}
```

__OPTIONS 절__

```sql
{구문 옵션 설명
예시)
OPTIONS(
    (text_col=column_name),
    (label_col=column_name),
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [overwrite={True|False}]
    )
}
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

{구문에서 사용되는 모든 매개변수들의 정의
예시)
- "text_col": 데이터 테이블에서 학습의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 3)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-4)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)
}

## __{구문 이름} 예시_

[{튜토리얼 제목}]({/ko/tutorials/thanosql_ml/튜토리얼/링크/})에서 "__{구문 이름}__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
{구문 쿼리 예시
예시)
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
  text_col='review',
  label_col='sentiment',
  epochs=1,
  batch_size=4,
  overwrite=True
  )
AS
SELECT *
FROM movie_review_train
}
```

{필요한 구문과 예시 추가}




<!-- 아래는 모델 구문 및 해당 옵션의 예입니다 -->
<!-- =================================== -->
<!-- MODEL SYNTAX Examples -->
<!-- BUILD -->
## __BUILD MODEL 구문__

"__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. "__BUILD MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 학습할 수 있습니다.

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
## __FIT MODEL 구문__

"__FIT MODEL__" 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 재학습할 수 있습니다. 이 때, 재학습에 사용하는 데이터의 라벨은 기존에 학습했을 때 사용한 라벨과 같아야 합니다.

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
## __PREDICT 구문__

"__PREDICT__" 구문을 사용하여 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

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
## __EVALUATE 구문__

"__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 사용하여 모델을 평가합니다.

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
## __CONVERT 구문__

"__CONVERT__" 구문은 데이터를 수치화한 벡터로 변환하고 이를 사용할 데이터 테이블에 추가합니다.

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
## __SEARCH IMAGE 구문__

"__SEARCH IMAGE__" 구문을 사용하여 원하는 이미지를 검색할 수 있습니다.

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


## __SEARCH TEXT 구문__

"__SEARCH TEXT__" 구문을 사용하여 원하는 문서를 검색할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH TEXT
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```


## __SEARCH KEYWORD 구문__

"__SEARCH KEYWORD__" 구문을 사용하여 키워드를 추출할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH KEYWORD 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```


## __SEARCH VIDEO 구문__

"__SEARCH VIDEO__" 구문을 사용하여 원하는 비디오를 검색할 수 있습니다.

```sql
query_statement:
    query_expr
    
SEARCH VIDEO 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```




<!-- =================================== -->
<!-- OPTIONS  -->
__OPTIONS 절__
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

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

1 
<!-- Albert & Electra -->
<!-- BUILD & FIT OPTIONS -->
- "text_col": 데이터 테이블에서 학습의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 3)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-4)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "text_col": 데이터 테이블에서 예측의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')

<!-- EVALUATE OPTIONS -->
- "text_col": 데이터 테이블에서 평가의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)


2
<!-- Wav2Vec2 -->
<!-- BUILD & FIT OPTIONS -->
- "audio_col": 데이터 테이블에서 학습의 대상이 될 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (str, default: 'audio_path')
- "text_col": 데이터 테이블에서 오디오의 스크립트를 담은 컬럼을 설정합니다. (default: "text")
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs":모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 5)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-4)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "audio_col": 데이터 테이블에서 예측의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')

<!-- EVALUATE OPTIONS -->
- "audio_col": 데이터 테이블에서 평가의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "text_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)


3
<!-- AutoML -->
<!-- BUILD & FIT OPTIONS -->
- "target_col": 데이터 테이블에서 분류 예측 모델에 목푯값이 되는 컬럼의 이름입니다. (str, default: 'target')
- "features_to_drop": 데이터 테이블에서 학습에 이용하지 못하는 컬럼을 설정합니다. (list[str], optional)
- "impute_type": 데이터 테이블에서 빈 값(NaNs)을 처리하는 방법을 설정합니다. (str, optional, 'simple'|'iterative', default: 'simple')
> "simple": 빈 값에 대해 범주형 변수는 최빈값으로, 연속형 변수는 평균으로 처리합니다.  
> "iterative": 빈 값에 대해 나머지 속성을 통해 예측하는 알고리즘을 적용하여 처리합니다.
- "datetime_attribs": 데이터 테이블에서 날짜에 해당하는 컬럼을 설정합니다. (list[str], optional)
- "outlier_method": 데이터 테이블에서 이상치를 처리하는 방법을 설정합니다. None일 경우, 데이터 테이블은 이상치를 포함합니다. (str, optional, 'knn'|'iso'|'pca', default: None)
> "knn": K-NN 기반 접근법으로 각 데이터 사이의 거리를 기반으로 비정상 샘플을 검출합니다.  
> "iso": 주어진 데이터 테이블에 대해서 Isolation Forest를 사용하여 트리 기반으로 랜덤하게 데이터 테이블을 분기하며 모든 관측치를 고립시키며 비정상 샘플을 검출합니다. (변수가 많은 데이터 세트에서도 효율적으로 작동합니다.)  
> "pca": 주어진 데이터 테이블에 대해서 Principal Component Analysis(PCA, 주성분 분석)를 이용하여 차원을 축소하고 복원을 하는 과정을 통해 비정상 샘플을 검출합니다.
- "time_left_for_this_task": 적합한 분류 예측 모델을 찾는데 소요되는 초 단위 시간을 의미합니다. 값이 클수록 적합한 모델을 찾을 가능성이 커집니다. (int, optional, default: 60)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')

<!-- EVALUATE OPTIONS -->
- "target_col": 데이터 테이블에서 분류 예측 모델에 목푯값이 되는 컬럼의 이름입니다. (str, default: 'target')


4
<!-- ConvNext, EfficientNet -->
<!-- BUILD & FIT OPTIONS -->
- "image_col": 데이터 테이블에서 학습의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 3)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-3)
- "input_size": 학습에 사용할 이미지의 사이즈를 설정합니다. (int, optional)
- "color": 학습에 사용할 이미지의 색상을 설정합니다. (str, optional, ‘RGB’|'GRAY’, default: ‘RGB’)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "image_col": 데이터 테이블에서 예측의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "input_size": 예측에 사용할 이미지의 사이즈를 설정합니다. (int, optional)

<!-- EVALUATE OPTIONS -->
- "image_col": 데이터 테이블에서 평가의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "input_size": 평가에 사용할 이미지의 사이즈를 설정합니다. (int, optional)


5
<!-- SimCLR -->
<!-- BUILD & FIT OPTIONS -->
- "image_col": 데이터 테이블에서 학습의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 256)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 5)
- "pretrained": 사전 학습된 ImageNet 가중치 사용 여부를 설정합니다. (bool, optional, True|False, default: False)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- CONVERT OPTIONS -->
- "image_col": 데이터 테이블에서 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 256)

<!-- SEARCH IMAGE BY IMAGE -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')



6. 
<!-- Whisper -->
<!-- PREDICT OPTIONS -->
- "audio_col": 데이터 테이블에서 예측의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "language": 오디오 파일의 주요 사용 언어를 설정합니다. ‘auto’일 경우 모델이 인식할 수 있는 99가지 언어 중 하나로 인식합니다. (str, default: 'auto')
- "task": 실행할 작업의 종류를 설정합니다. (str, 'transcribe'|'translate', default: 'transcribe')
> "transcribe": 음성 인식을 합니다.  
> "translate": 인식된 음성을 영어로 출력합니다. 이 과정은 ‘한국어 음성‘을 바로 ‘영어 텍스트‘로 번역하는 것으로, 중간에 ‘한국어 텍스트’를 거치지 않는 다는 점이 일반적인 번역 태스크와 다릅니다.
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')

<!-- EVALUATE OPTIONS -->
- "audio_col": 데이터 테이블에서 평가의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "language": 오디오 파일의 주요 사용 언어를 설정합니다. ‘auto’일 경우 모델이 인식할 수 있는 99가지 언어 중 하나로 인식합니다. (str, default: 'auto')
- "text_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'text')


7.
<!-- ViLT -->
<!-- PREDICT OPTIONS -->
- "image_col": 데이터 테이블에서 예측의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: "image_path")
- "question": 예측에 사용할 질문 내용입니다. (str)
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: "predict_result")


8. 
<!-- CLIP -->
<!-- CONVERT OPTIONS -->
- "image_col": 데이터 테이블에서 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "text_col": 데이터 테이블에서 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "convert_type": 수치화할 파일의 종류를 설정합니다. (str, 'image'|'text', default: 'image')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

<!-- SEARCH IMAGE BY TEXT -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')


9. 
<!-- XCLIP -->
<!-- CONVERT OPTIONS -->
- "video_col": 데이터 테이블에서 비디오의 경로를 담은 컬럼의 이름입니다. (str, default: 'video_path')
- "text_col": 데이터 테이블에서 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "convert_type": 수치화할 파일의 종류를 설정합니다. (str, 'video'|'text', default: 'video')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

<!-- SEARCH VIDEO BY TEXT -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')


10.
<!-- SBERT -->
<!-- BUILD & FIT OPTIONS -->
- "text_col": 데이터 테이블에서 학습의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 1)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 3e-5)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- CONVERT OPTIONS -->
- "text_col": 데이터 테이블에서 수치화의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

<!-- SEARCH TEXT BY TEXT -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')

<!-- SEARCH KEYWORD -->
- "lang": 사용할 언어를 설정합니다. (str, optional, 'ko'|'en' default: 'ko')
- "text_col": 데이터 테이블에서 키워드 추출의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "ngram_range": 키워드의 최소 단어 수와 최대 단어 수를 정합니다. (list[int, int], optional, default: [1, 2])
- "top_n": 추출할 키워드의 수입니다. 유사도가 높은 순서대로 출력합니다. (int, optional, default: 5)
- "diversity": 추출될 키워드의 다양성입니다. 높을 수록 기존에 추출된 키워드와 유사한 키워드는 다시 추출되지 않습니다. 0 <= diversity <= 1 (float, optional, default: 0.5)
- "use_stopwords": 큰 의미가 없는 단어를 제외할지 정합니다. (bool, optional, True|False, default: True)
- "threshold": 추출할 키워드의 유사도 수치의 최소값입니다. (float, optional, default: 0.0)