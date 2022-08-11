########### EVALUATE 구문 정리 #############

########## ConvNeXt, EfficientNet ##########
### __EVALUATE USING 쿼리 구문__

이 "__EVALUATE USING__" 쿼리 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE USING__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

``` sql
query_statement:
    query_expr

EVALUATE USING [expression]
OPTIONS (
    expression
    )
AS
(query_expr)
```

 __OPTIONS 절__

```sql
OPTIONS(
    (image_col = column_name),
    [batch_size = VALUE]
    )
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "image_col" : 데이터 테이블에서 이미지의 경로를 담은 컬럼을 설정합니다. (DEFAULT : "image")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


########## Albert, Electra ##########
### __EVALUATE USING 쿼리 구문__

이 "__EVALUATE USING__" 쿼리 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE USING__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

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

 __OPTIONS 절__

```sql
OPTIONS(
    (text_col = column_name),
    [batch_size = VALUE]
    )
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col" : 데이터 테이블에서 분류의 대상이 될 텍스트를 담은 컬럼을 설정합니다. (DEFAULT : "text")

- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


########## Wav2Vec2 ##########
### __EVALUATE USING 쿼리 구문__

이 "__EVALUATE USING__" 쿼리 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE USING__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

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

 __OPTIONS 절__

```sql
OPTIONS(
    (audio_col = column_name),
    (text_col = column_name),
    [batch_size = VALUE]
    )
```

"__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (DEFAULT : "audio_path")
- "text_col" : 데이터 테이블에서 오디오의 스크립트를 담은 컬럼을 설정합니다. (DEFAULT : "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)