# __ThanoSQL 알고리즘 쿼리 구문__

쿼리 구문은 표현식을 한 개 이상 검색하고 계산된 결과 테이블을 반환합니다. 이 페이지에서는 ThanoSQL에서 사용하는 알고리즘 쿼리에 관한 구문을 설명합니다.

**[이전 문서 - 결과 출력하기](/how-to_guides/modelling/PRINT_SYNTAX/)**

__표기법 규칙__

- 대괄호 `[]`는 선택적 절을 나타냅니다.
- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.
- 중괄호 { }는 옵션 조합을 묶는 데 사용됩니다.
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된
목록으로 반복될 수 있음을 의미합니다.
- VALUE는 숫자값을 의미합니다.

!!! note ""
    __리터럴__ : 고정되거나 변경할 수 없는 값을 의미하며 상수(constant)라고도 불립니다.
    > 각 리터럴은 테이블에서 열과 같은 특별한 자료형을 가지고 있다.

## __1. ConvNeXt, EfficientNet 모델__

### __BUILD MODEL 쿼리 구문__

이 "__BUILD MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 개발할 수 있습니다.
"__BUILD MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다.

``` sql
query_statement:
    query_expr

BUILD MODEL [expression]
USING {ConvNeXt_Tiny | ConvNeXt_Base | EfficientNetV2S | EfficientNetV2M}
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [image_col = column_name],
    [label_col = column_name],
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "image_col" : 데이터 테이블에서 이미지의 경로를 담은 열을 설정합니다. (DEFAULT: "image")
- "label_col" : 데이터 테이블에서 라벨의 경로를 담은 열을 설정합니다. (DEFAULT: "label")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (DEFAULT : 3)
- "learning_rate" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : ConvNeXt=0.0001, EfficientNetV2=0.001)


#### __BUILD MODEL 쿼리 구문 예시__

[고양이와 강아지 분류하는 이미지 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_convNext.md)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL my_image_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image',
  label_col='label',
  epochs=1
  )
AS
SELECT *
FROM cat_and_dog_train
```

### __FIT MODEL 쿼리 구문__

이 "__FIT MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 재학습할 수 있습니다. 이 때, 재학습에 사용하는 데이터의 라벨은 기존에 학습했을 때 사용한 라벨과 같아야 합니다.

``` sql
query_statement:
    query_expr

FIT MODEL [expression]
USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [image_col = column_name],
    [label_col = column_name],
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "image_col" : 데이터 테이블에서 이미지의 경로를 담은 열을 설정합니다. (DEFAULT: "image")
- "label_col" : 데이터 테이블에서 라벨의 경로를 담은 열을 설정합니다. (DEFAULT: "label")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (DEFAULT : 3)
- "learning_rate" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : ConvNeXt=0.0001, EfficientNetV2=0.001)



### __PREDICT USING 쿼리 구문__

이 "__PREDICT USING__" 쿼리 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다."__PREDICT USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

``` sql
query_statement:
    query_expr

PREDICT USING [expression]
OPTIONS (
    image_col = 'image',
    )
AS
(query_expr)
```

#### __PREDICT USING 쿼리 구문 예시__
[고양이와 강아지 분류하는 이미지 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_convNext.md)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING my_image_classifier
OPTIONS (image_col='image')
AS
SELECT *
FROM cat_and_dog_test
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [image_col = column_name],
    [batch_size = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "image_col" : 데이터 테이블에서 이미지의 경로를 담은 열을 설정합니다. (DEFAULT: "image")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


### __EVALUATE USING 쿼리 구문__

이 "__EVALUATE USING__" 쿼리 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

``` sql
query_statement:
    query_expr

EVALUATE USING [expression]
OPTIONS (
    image_col = 'image',
    )
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [image_col = column_name],
    [label_col = column_name],
    [batch_size = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "image_col" : 데이터 테이블에서 이미지의 경로를 담은 열을 설정합니다. (DEFAULT: "image")
- "label_col" : 데이터 테이블에서 이미지의 경로를 담은 열을 설정합니다. (DEFAULT: "label")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)

<br>

## __2. Albert, Electra 모델__

### __BUILD MODEL 쿼리 구문__

이 "__BUILD MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 개발할 수 있습니다.
"__BUILD MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다.

``` sql
query_statement:
    query_expr

BUILD MODEL [expression]
USING {AlbertKo | AlbertEn | ElectraKo | ElectraEn}
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [text_col = column_name],
    [label_col = column_name],
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col" : 데이터 테이블에서 분류의 대상이 될 텍스트를 담은 열을 설정합니다. (DEFAULT: "text")
- "label_col" : 데이터 테이블에서 라벨의 경로를 담은 열을 설정합니다. (DEFAULT: "label")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (DEFAULT : 3)
- "learning_rate" : 모델의 학습률입니다. (DEFAULT : 0.0001)


#### __BUILD MODEL 쿼리 구문 예시__

[영화 리뷰에 내포된 감정(긍정 또는 부정) 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_Electra.md)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
  text_col='review',
  label_col='sentiment',
  epochs=1,
  batch_size=4
  )
AS
SELECT *
FROM imdb_train
```

### __FIT MODEL 쿼리 구문__

이 "__FIT MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 재학습할 수 있습니다. 이 때, 재학습에 사용하는 데이터의 라벨은 기존에 학습했을 때 사용한 라벨과 같아야 합니다.

``` sql
query_statement:
    query_expr

FIT MODEL [expression]
USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [text_col = column_name],
    [label_col = column_name],
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col" : 데이터 테이블에서 분류의 대상이 될 텍스트를 담은 열을 설정합니다. (DEFAULT: "text")
- "label_col" : 데이터 테이블에서 라벨의 경로를 담은 열을 설정합니다. (DEFAULT: "label")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (DEFAULT : 3)
- "learning_rate" : 모델의 학습률입니다. (DEFAULT : 0.0001)



### __PREDICT USING 쿼리 구문__

이 "__PREDICT USING__" 쿼리 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다."__PREDICT USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

``` sql
query_statement:
    query_expr

PREDICT USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __PREDICT USING 쿼리 구문 예시__
[영화 리뷰에 내포된 감정(긍정 또는 부정) 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_Electra.md)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (text_col='review')
AS
SELECT *
FROM imdb_test
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [text_col = column_name],
    [batch_size = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col" : 데이터 테이블에서 분류의 대상이 될 텍스트를 담은 열을 설정합니다. (DEFAULT: "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


### __EVALUATE USING 쿼리 구문__

이 "__EVALUATE USING__" 쿼리 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

``` sql
query_statement:
    query_expr

EVALUATE USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [text_col = column_name],
    [label_col = column_name],
    [batch_size = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col" : 데이터 테이블에서 분류의 대상이 될 텍스트를 담은 열을 설정합니다. (DEFAULT: "text")
- "label_col" : 데이터 테이블에서 이미지의 경로를 담은 열을 설정합니다. (DEFAULT: "label")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)

<br>

## __3. Wav2Vec2 모델__

### __BUILD MODEL 쿼리 구문__

이 "__BUILD MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 개발할 수 있습니다.
"__BUILD MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다.

``` sql
query_statement:
    query_expr

BUILD MODEL [expression]
USING {Wav2Vec2Ko | Wav2Vec2En}
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [audio_col = column_name],
    [text_col = column_name],
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 열을 설정합니다. (DEFAULT: "audio")
- "text_col" : 데이터 테이블에서 오디오의 스크립트를 담은 열을 설정합니다. (DEFAULT: "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 5)
- "learning_rate" : 모델의 학습률입니다. (DEFAULT : 0.0001)


#### __BUILD MODEL 쿼리 구문 예시__

[영화 리뷰에 내포된 감정(긍정 또는 부정) 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_Electra.md)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL tutorial_audio_recognition
USING Wav2Vec2En
OPTIONS (
    audio_col='audio',
    text_col='text',
    epochs=1,
    batch_size=8)
AS
SELECT *
FROM librispeech_train
```

### __FIT MODEL 쿼리 구문__

이 "__FIT MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 재학습할 수 있습니다.

``` sql
query_statement:
    query_expr

FIT MODEL [expression]
USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [audio_col = column_name],
    [text_col = column_name],
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 열을 설정합니다. (DEFAULT: "audio")
- "text_col" : 데이터 테이블에서 오디오의 스크립트를 담은 열을 설정합니다. (DEFAULT: "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 5)
- "learning_rate" : 모델의 학습률입니다. (DEFAULT : 0.0001)



### __PREDICT USING 쿼리 구문__

이 "__PREDICT USING__" 쿼리 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다."__PREDICT USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

``` sql
query_statement:
    query_expr

PREDICT USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __PREDICT USING 쿼리 구문 예시__
[영화 리뷰에 내포된 감정(긍정 또는 부정) 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_Electra.md)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (audio_col='audio')
AS
SELECT *
FROM librispeech_test
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [audio_col = column_name],
    [batch_size = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 열을 설정합니다. (DEFAULT: "audio")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


### __EVALUATE USING 쿼리 구문__

이 "__EVALUATE USING__" 쿼리 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

``` sql
query_statement:
    query_expr

EVALUATE USING [expression]
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

#### __OPTIONS 절__

```sql
OPTIONS(
    [audio_col = column_name],
    [text_col = column_name],
    [batch_size = VALUE]
)
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 열을 설정합니다. (DEFAULT: "audio")
- "text_col" : 데이터 테이블에서 오디오의 스크립트를 담은 열을 설정합니다. (DEFAULT: "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)

