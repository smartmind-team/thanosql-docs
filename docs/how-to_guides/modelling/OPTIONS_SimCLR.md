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

## __1. SimCLR 임베딩 추출 모델__

### __BUILD MODEL 쿼리 구문__ 

이 "__BUILD MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. 
"__BUILD MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다. 

``` sql
query_statement:
    query_expr

BUILD MODEL [expression] 
USING AutomlClassifier
OPTIONS (
    expression [ , ...]
)
AS 
(query_expr)
``` 

#### __OPTIONS 절__

```sql
OPTIONS(
    [max_epochs = VALUE],    
    [batch_size = VALUE]    
)
```

"__OPTIONS__" 절은 SimCLR 임베딩 모델의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "max_epochs" : 모델학습 횟수를 설정합니다. (DEFAULT: 5)
- "batch_size" : 학습 때 사용되어지는 데이터 묶음 속의 데이터 수를 설정합니다. (DEFAULT: 256)



#### __BUILD MODEL 쿼리 구문 예시__

[이미지로 유사한 이미지 검색하기](/tutorials/thanosql_search/image_search/simclr_image_search/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
BUILD MODEL mnist_model
USING SimCLR
OPTIONS (max_epochs=5)
AS 
SELECT * 
FROM mnist_dataset
```

### __CONVERT USING 쿼리 구문__
이 "__CONVERT USING__" 쿼리 구문을 사용하여 기존 이미지들의 경로가 포함되어 있는 데이터 세트를 사용하여 임베딩 된 결과를 기존의 데이터셋에 새로운 칼럼으로 저장합니다. 새로운 임베딩 모델을 사용할때마다 새로운 임베딩 칼럼이 추가되어 임베딩 결과 별 비교가 용이합니다.  


```sql
CONVERT USING [expression]
OPTIONS(
        expression [ , ...] 
        )
AS 
(query_expr)
``` 


#### __OPTIONS 절__

```sql
OPTIONS(
    (table_name = VALUE)  
)
```

"__OPTIONS__" 절은 SimCLR 임베딩 모델의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "table_name" : 새로운 임베딩 결과를 저장할 테이블 이름을 설정합니다. (DEFAULT: 5)


#### __CONVERT USING 쿼리 구문 예시__

[이미지로 유사한 이미지 검색하기](/tutorials/thanosql_search/image_search/simclr_image_search/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
CONVERT USING mnist_model
OPTIONS(
    table_name= "mnist_dataset"
    )
AS 
SELECT * 
FROM mnist_dataset
```

### __CREATE TABLE 쿼리 구문__

이 "__CREATE TABLE__" 쿼리 구문을 사용하여 이미지별 폴더경로 정보가 포함되어 있는 테이블 없이도 이미지 폴더 경로를 사용하여 임베딩 변환이 가능합니다. 
"__CREATE TABLE__" 표현식은 FROM 뒤에 나오는 이미지 폴더 경로의 이미지 파일들을 임베딩하여 테이블로 저장합니다. 

``` sql
query_statement:
    query_expr

CREATE TABLE [expression] 
USING [expression]
OPTIONS (
    expression [ , ...]
)
FROM
(query_expr)
``` 

#### __OPTIONS 절__

```sql
OPTIONS(
    (path_type = {'folder'|'file'}),    
    (data_type = {'image'|'audio'|'video'}),
    (file_type = LIST)    
)
```

"__OPTIONS__" 는 이미지 수치화를 위한 이미지 파일의 속성값들을 정의합니다. 각 매개변수의 의미는 아래와 같습니다. 

- "path_type" : 데이터가 저장되어 있는 파일 경로의 타입을 설정합니다.(folder|file)
- "data_type" : 입력하는 비정형 데이터의  종류를 설정합니다. (image|audio|video)
- "file_type" : 대상 파일의 확장자를 리스트로 정의하여 줍니다. (ex. ['.jpg'], ['.png'])



#### __CREATE TABLE 쿼리 구문 예시__

[이미지로 유사한 이미지 검색하기](/tutorials/thanosql_search/image_search/simclr_image_search/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
CREATE TABLE mnist_embds
USING mnist_model 
OPTIONS(
    path_type='folder', 
    data_type='image',
    file_type=['.jpg']
    ) 
FROM '/data/development-model/data/mnist/MNIST_DATASET/train_data/'
```
