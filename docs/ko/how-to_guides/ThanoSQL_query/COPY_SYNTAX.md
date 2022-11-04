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

!!! note "__Note__"    
    - COPY 구문을 사용하여 ThanoSQL DB에 데이터 테이블을 생성하실 수 있습니다. COPY 구문의 OPTIONS로는 overwrite 상태만 지정할 수 있으며 overwrite 상태의 default 값은 False 입니다.  
    - overwrite가 True일 경우, ThanoSQL DB에 기존 데이터 테이블을 삭제하고 새로운 데이터 테이블을 같은 이름으로 만드실 수 있습니다. 그러지 않을 경우, 같은 이름의 데이터 테이블을 생성하실 수 없습니다.

## __3. COPY 예시__ 

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "data/example.csv"
```




