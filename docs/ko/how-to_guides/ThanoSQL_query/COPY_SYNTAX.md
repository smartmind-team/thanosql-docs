---
title: COPY
---

# __COPY__

## __1. COPY  문__

사용자는 "__COPY__" 구문을 사용하여 데이터 파일을 ThanoSQL 워크스페이스 DB의 테이블로 생성/복사할 수 있습니다. 

!!! note "__지원 가능한 데이터 파일 형식__"
    - csv
    - pkl (pandas로 생성한 pkl 파일만 지원)
    - xls, xlsx, xlsm, xlsb

!!! warning "__주의사항__" 
    - 데이터 파일의 컬럼 이름은 소문자, 숫자 그리고 _(밑줄)만을 허용합니다.
    - pkl 파일의 경우 pandas로 생성했을 때만 지원합니다. 


## __2. COPY 구문__

"__COPY__" 구문은 데이터 파일을 ThanoSQL 워크스페이스 DB의 테이블로 생성/복사할 수 있습니다.

```sql
%%thanosql
COPY [ThanoSQL 워크스페이스 DB에 저장할 데이터 테이블 이름] 
OPTIONS (
    overwrite=True
) 
FROM  
[워크스페이스에 있는 데이터 파일의 절대 경로]
```

!!! note "__Note__"    
    - "__OPTIONS__" 쿼리 구문을 통해 __COPY__ 에 사용할 옵션을 지정합니다.  
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 





