---
title: {함수 구문 이름}
---

# __{함수 구문 이름}__

## __1. {함수 구문 이름} 문__

"__{함수 구문 이름}__" 구문을 사용하여 {구문 설명}.

예시)
사용자는 "__Compute__" 구문을 사용하여 ThanoSQL의 "__Compute__" 함수를 통해 데이터를 처리합니다.

## __2. {함수 구문 이름} 함수__

<!-- 대부분의 경우 각 함수 구문 모듈에는 둘 이상의 함수가 있습니다. -->

### __2-1. {서브 함수 이름}__

"__{서브 함수 이름}__"은 {설명}.

예시)
"__groupby_mean__"은 데이터 테이블에 임베딩과 라벨 열이 존재하면 라벨별로 그룹화하고 임베딩 값을 평균화하는 기능입니다.

__{서브 함수 이름} 구문__

```sql
{함수 구문 쿼리 설명
예시)
FUNCTION {함수 구문 이름}.{서브 함수 이름}
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} }
}
```

__OPTIONS 절__

```sql
{함수 구문 옵션 설명
예시)
OPTIONS(
    (label_col=column_name),
    (emb_col=column_name),
    [result_col=expression]
    )
}
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

{구문에서 사용되는 모든 매개변수들의 정의
예시)
- "label_col": 데이터 테이블에서 라벨의 정보를 담은 컬럼의 이름입니다. (str)
- "emb_col": 데이터 테이블에서 수치화 정보를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 임베딩 값들의 평균을 담을 컬럼 이름을 설정합니다. (str, optional, default: 'mean_emb_result')
}

__{서브 함수 이름} 예시__

```sql
%%thanosql
{예시)
FUNCTION compute.groupby_mean
OPTIONS (
    label_col='label',
    emb_col='convert_result',
    result_col='result_col'
    )
AS
SELECT * FROM mnist_train_test
}
```

### __2-2. {다른 서브 함수가 존재하는 경우}__

<!-- 형식은 2-1과 동일해야 하며 이는 각 모듈의 모든 서브 함수에 대해 수행되어야 합니다. -->