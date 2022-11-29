---
title: SBERT
---

# __SBERT__

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


## __BUILD MODEL 구문__

이 "__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다.
"__BUILD MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다.

``` sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { SBERTKo | SBERTEn }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```
!!!faq ""
    - 본 쿼리를 통해서 USING 뒤에 나오는 베이스 인공지능 모델을 model_name_expression 이름으로 저장합니다.

__OPTIONS 절__

```sql
OPTIONS(
    (text_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [train = {True | False}]
    [overwrite = {True | False}]
    )
```

"__OPTIONS__" 절은 SBERT의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col": 데이터 테이블에서 영화 리뷰 데이터를 담은 컬럼을 설정합니다.
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (default: 16)
- "epochs": 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (default: 1)
- "learning_rate": 모델의 학습률입니다. (default: 3e-5)
- "train": 사전훈련된 모델을 추가로 학습할지를 설정합니다. False일 경우 사전훈련된 모델을 추가로 학습하지 않고 그대로 사용합니다. (default: True)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (default: False)

 __BUILD MODEL 예시__

[텍스트로 텍스트 검색하기](/tutorials/thanosql_search/search_text_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL nsmc_text_search_model
USING SBERTKo
OPTIONS (
    text_col="document",
    overwrite=True
)
AS
SELECT *
FROM nsmc_train
```

## __CONVERT 구문__

"__CONVERT__" 구문은 기존에 존재하던 테이블에서 텍스트 데이터를 수치화한 벡터로 변환하고 이를 사용할 데이터 테이블에 추가합니다.

```sql
CONVERT USING (model_name_expression)
OPTIONS(
    (table_name = expression),
    (text_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

!!!faq ""
    - 본 쿼리를 통해서 USING 뒤에 나온 모델인 model_name_expression을 사용합니다.

__OPTIONS 절__

```sql
OPTIONS(
    (table_name=expression),
    (text_col=column_name),
    [batch_size=VALUE]
)
```

"__OPTIONS__" 절은 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "table_name": 새로 만들어질 테이블의 이름입니다.
- "text_col": 테이블에서 영화 리뷰 데이터를 담고 있는 컬럼의 이름입니다.
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (default: 16)


__CONVERT 예시__

[텍스트로 텍스트 검색하기](/tutorials/thanosql_search/search_text_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.


```sql
%%thanosql
CONVERT USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    table_name="nsmc_test",
    batch_size=32
    )
AS 
SELECT *
FROM nsmc_test
```

## __SEARCH 구문__

"__SEARCH__" 구문을 사용하여 수치화을 생성한 테이블에서 원하는 문서를 검색할 수 있습니다.

``` sql
SEARCH TEXT (text = expression)
USING (model_name_expression)
AS 
(query_expr)
```

!!! note ""
    - 사용자로부터 text를 입력으로 받아야 합니다. 입력은 string (예: "기분이 좋아지는 작품", "영화 내용이 불만족스러웠다")이어야 합니다.

!!! faq ""
    - 본 쿼리를 통해서 USING 뒤에 나온 모델인 model_name_expression을 사용합니다.

"__SEARCH__" 구문을 사용하여 수치화을 생성한 테이블에서 키워드를 추출할 수 있습니다.

``` sql
%%thanosql
SEARCH KEYWORD
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
```

!!! faq ""
    - 본 쿼리를 통해서 USING 뒤에 나온 모델인 model_name_expression을 사용합니다.

__OPTIONS 절__

```sql
OPTIONS (
    (text_col = column_name),
    [ngram_range = [int, int]],
    [use_stopwords = {True | False}]
    [lang = {en | ko}]
    [top_n = VALUE]
    [diversity = VALUE]
    [threshold = VALUE]
    )
```

"__OPTIONS__" 절은 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col": 테이블에서 영화 리뷰 데이터를 담고 있는 컬럼의 이름입니다.
- "ngram_range": 키워드의 최소 단어 수와 최대 단어 수를 정합니다. (list[int, int], default: [1, 2])
- "use_stopwords": 큰 의미가 없는 단어를 제외할지 정합니다. (default: True)
- "lang": en, ko
- "top_n": 추출할 키워드의 수입니다. 유사도가 높은 순서대로 출력합니다. (int, default: 5)
- "diversity": 추출될 키워드의 다양성입니다. 높을 수록 기존에 추출된 키워드와 유사한 키워드는 다시 추출되지 않습니다. 0 <= diversity <= 1 (float, default: 0.5)
- "threshold": 추출할 키워드의 유사도 수치의 최소값입니다. (float, default: 0.0)

__SEARCH 예시__

[텍스트로 텍스트 검색하기](/tutorials/thanosql_search/search_text_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SEARCH KEYWORD
USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    ngram_range=[1, 3],
    use_stopwords=True
    )
AS (
    SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
    FROM (
        SEARCH TEXT text="가볍게 볼 수 있는 코미디 영화"
        USING nsmc_text_search_model
        AS 
        SELECT * 
        FROM nsmc_test
        )
    ORDER BY score DESC 
    LIMIT 10
)
```