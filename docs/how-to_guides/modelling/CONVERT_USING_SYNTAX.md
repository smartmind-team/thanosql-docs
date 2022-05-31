# __비정형 특성 추가하기 (CONVERT USING)__

**[이전 문서 - 비정형 데이터 변환하기](/how-to_guides/modelling/CREATE_TABLE_SYNTAX/)**  
**[다음 문서 - 비정형 데이터 검색하기](/how-to_guides/modelling/SEARCH_SYNTAX/)**

## __1.CONVERT USING 쿼리 구문 개요__

사용자는 "__CONVERT USING__"  구문은 이미지, 비디오, 음성 등 비정형 데이터의 정보를 이용해서 임베딩 알고리즘을 사용하여 벡터 형식으로 변환하고 이 값을 사용할 데이터 세트에 추가합니다.

## __2.CONVERT USING 쿼리 구문__

```sql
CONVERT USING [사용할 인공지능 모델]
OPTIONS(
        table_name=[저장될 테이블 명]
        )
AS 
[사용할 데이터 세트]
```

## __3.CONVERT USING 쿼리 구문 예시__ 

### __3.1`Color_descriptor` 알고리즘을 사용한 이미지 임베딩__ 
아래 예는 [비정형 데이터 변환하기(CREATE TABLE)](../modelling/CREATE_TABLE_SYNTAX.md)에서 만들었던 색 특징 추출 모델을 사용하여 임베딩 한결과를 "color_descriptor_table_test" 이름으로 ThanoSQL DB상에 저장합니다.

```sql
%%thanosql
CONVERT USING Color_Descriptor
OPTIONS(
        table_name= "color_descriptor_table_test"
        )
AS 
SELECT * 
FROM color_descriptor_table_test
```

![image](/img/CONVERT_USING_img1.png) <br>

## __3.2`clip_en` 알고리즘을 사용한 이미지 임베딩__
아래 예는 `clip_en` 알고리즘을 사용하여 임베딩한 결과를 "mnist_dataset" 이름으로 ThanoSQL DB상에 저장합니다.
```sql
%%thanosql
CONVERT USING clip_en
OPTIONS(
        image_col='img_path', 
        table_name='mnist_dataset', 
        batch_size=128
        )
AS 
SELECT * 
FROM mnist_dataset
```