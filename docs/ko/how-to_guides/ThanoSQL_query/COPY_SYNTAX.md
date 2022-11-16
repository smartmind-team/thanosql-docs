---
title: COPY
---

# __COPY__

## __1. COPY  문__

사용자는 "__COPY__" 구문을 사용하면 현재 ThanoSQL 워크스페이스에 있는 데이터 파일들, 데이터 폴더, 그리고 작업중인 pandas 데이터프레임을 ThanoSQL DB 안의 데이터 테이블로 생성할 수 있습니다. 

!!! note "__지원 가능한 데이터 파일 형식__"
    - csv
    - pkl, pickle
    - xls, xlsx, xlsm, xlsb

!!! warning "__주의사항__" 
    - 데이터 파일 및 데이터프레임 컬럼 이름은 소문자, 숫자 그리고 _(밑줄)만을 허용합니다.

## __2. COPY 구문__

"__COPY__" 구문은 ThanoSQL 워크스페이스에 있는 데이터 파일들, 데이터 폴더, 그리고 작업중인 pandas 데이터프레임을 ThanoSQL DB 안의 데이터 테이블로 생성할 수 있습니다. 데이터프레임 사용 방법은 아래의 예시 중 __3-3. Pandas 데이터프레임 사용시__ 에서 확인하실 수 있습니다. 

```sql
%%thanosql
COPY [ThanoSQL DB에 저장할 데이터 테이블 이름] 
OPTIONS (
    overwrite=True
) 
FROM  
[데이터 파일 경로 | 데이터 폴더 경로]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "overwrite":동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (True|False, DEFAULT : False)

## __3. COPY 예시__ 

### __3-1. 데이터 파일 사용시__

아래 예는 "COPY" 구문을 사용하여 사용자가 정의한 example.csv 파일을 사용합니다. 정의된 데이터 파일은 ThanoSQL Engine을 통해 데이터베이스의 테이블로 생성됩니다. 

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "data/example.csv"
```

### __3-2. 데이터 폴더 사용시__

아래 예는 "COPY" 구문을 사용하여 사용자가 정의한 diet_image_data 폴더를 사용합니다. 정의된 데이터 폴더는 ThanoSQL Engine을 통해 데이터베이스의 테이블로 생성됩니다. 

!!! note "__데이터 폴더 COPY 사용법__"
    - 이미지, 오디오, 혹은 비디오가 있는 데이터 폴더의 경로를 입력하면 폴더 내의 각각의 파일들을 자동으로 행으로 변화하여 데이터 테이블로 재생성합니다. 

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "diet_image_data/"
```
 
### __3-3. Pandas 데이터프레임 사용시__

아래 예는 "COPY" 구문을 사용하여 사용자가 정의한 Pandas 데이터프레임을 사용합니다. 정의된 데이터프레임은 ThanoSQL Engine을 통해 데이터베이스의 테이블로 생성됩니다. 

#### Pandas 데이터프레임 준비
```python
# Pandas Dataframe 만듭니다 
df = pd.read_csv("./diet_image_data/sample.csv")
# 데이터프레임은 JSON으로 변환 되어야 하며, 'orient'는 꼭 'records'로 명시합니다
df_in_json = df.to_json(orient="records")

# f-string을 사용하여 데이터프레임의 변수명인 'df_in_json'을 COPY 구문으로 감싸줍니다
copy_pandas_df = f'''
COPY mytable 
OPTIONS (
    overwrite=True
)
FROM '{df_in_json}'
'''
```

#### COPY 구문으로 Pandas 데이터프레임 사용하기 

```sql
%thanosql $copy_pandas_df
```

!!! warning "__Warning__"
    - Pandas 데이터프레임은 __COPY__ 구문으로 감싸기 전에 JSON으로 변환하여야 합니다. 
    - __${변수_이름}__ 는  __%thanosql__ 뒤에 명시되어야 합니다.

