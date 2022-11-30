---
title: CLIP
---

# __CLIP__

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

## __CONVERT 구문__

"__CONVERT__" 구문은 기존에 존재하던 테이블에서 이미지 데이터를 수치화한 벡터로 변환하고 이를 사용할 데이터 테이블에 추가합니다.

```sql
CONVERT USING clip_en
OPTIONS(
    (table_name = expression),
    (image_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

!!!faq ""
    - 본 쿼리를 통해서 USING 뒤에 나온 모델인 clip_en을 사용합니다. clip의 경우 현재 Build를 제공하지 않기 때문에 베이스 모델인 clip_en을 사용합니다.

__OPTIONS 절__

```sql
OPTIONS(
    (table_name=expression),
    (image_col=column_name),
    [batch_size=VALUE]
)
```

"__OPTIONS__" 절은 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "table_name": 새로 만들어질 테이블의 이름입니다.
- "image_col": 테이블에서 이미지의 경로를 담고 있는 컬럼의 이름입니다. (default: 'image_path')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (default: 16)


__CONVERT 예시__

[텍스트로 이미지 검색하기](/ko/tutorials/thanosql_search/search_image_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.


```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path", 
    table_name="unsplash_data", 
    batch_size=128
    )
AS 
SELECT * 
FROM unsplash_data
```

## __SEARCH 구문__

"__SEARCH__" 구문을 사용하여 수치화을 생성한 테이블에서 원하는 이미지를 검색할 수 있습니다.

``` sql
SEARCH IMAGE 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```

!!! faq ""
    - 본 쿼리를 통해서 USING 뒤에 나온 모델인 clip_en을 사용합니다. clip의 경우 현재 Build를 제공하지 않기 때문에 베이스 모델인 clip_en을 사용합니다.

__OPTIONS 절__

```sql
OPTIONS (
    (search_input_type = {image | text | audio | video}),
    [search_input = expression],
    [emb_col = embedded_column_name]
    [column_name = column_name_to_be_saved_as]
    )
```

"__OPTIONS__" 절은 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "search_input_type": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입
- "search_input": 검색할 때 사용할 입력값 
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼
- "column_name": 데이터 테이블에서 검색 결과를 담을 컬럼 이름 (default: "search_result")


__SEARCH 예시__

[텍스트로 이미지 검색하기](/ko/tutorials/thanosql_search/search_image_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SEARCH IMAGE
USING tutorial_search_clip
OPTIONS (
    search_input_type="text",
    search_input="a black cat",
    emb_col="convert_result",
    column_name="search_result"
)
AS 
SELECT * 
FROM unsplash_data
```