---
title: COPY
---

# __COPY__

## __1. COPY  문__

사용자는 "__COPY__" 구문을 사용하면 현재 ThanoSQL 워크스페이스에 있는 데이터 파일들을 ThanoSQL DB 안의 데이터 테이블로 생성할 수 있습니다. 

!!! note "__지원 가능한 데이터 파일 형식__"
    - csv
    - pkl
    - xls, xlsx, xlsm, xlsb

!!! warning "__주의사항__" 
    - 데이터 파일의 컬럼 이름은 소문자, 숫자 그리고 _(밑줄)만을 허용합니다.

## __2. COPY 구문__

"__COPY__" 구문은 ThanoSQL 워크스페이스에 있는 데이터 파일들을 ThanoSQL DB 안의 데이터 테이블로 생성할 수 있습니다.

```sql
%%thanosql
COPY [ThanoSQL DB에 저장할 데이터 테이블 이름] 
OPTIONS (
    overwrite=True
) 
FROM  
[워크스페이스에 있는 데이터 파일의 절대 경로]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "overwrite":동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (True|False, DEFAULT : False)

## __3. COPY 예시__ 

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "data/example.csv"
```




