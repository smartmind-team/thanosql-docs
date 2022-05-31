# __비정형 데이터 변환하기 (CREATE TABLE)__

**[이전 문서 - 저장한 모델 확인하기](/how-to_guides/modelling/LIST_SYNTAX/)**  
**[다음 문서 - 비정형 특성 추가하기](/how-to_guides/modelling/CONVERT_USING_SYNTAX/)**

## __1. CREATE TABLE 쿼리 구문 개요__ 

사용자는 "__CREATE TABLE__" 구문을 사용하여 비정형 데이터 (이미지, 음성 그리고 영상)를 임베딩 알고리즘을 사용하여 벡터 형식으로 변환한 데이터 테이블을 생성할 수 있습니다.

## __2. CREATE TABLE 구문__ 

```sql
CREATE TABLE [사용자 지정 데이터 테이블 이름]
USING [사용할 인공지능 모델]
AS [사용할 데이터 세트]
```


## __3. CREATE TABLE 구문 예시__ 

아래 예는 '/data/thanosAlgo/image_search/junyoung_test/' `Color_descriptor`라는 속성 추출 인공지능 모델을 사용하여 경로에 존재하는 이미지 파일들을 사용자가 지정한 이름인 `color_descriptor_table_test` 라는 데이터 테이블로 ThanoSQL 데이터베이스 내에 생성합니다. 

```sql
%%thanosql
CREATE TABLE color_descriptor_table_test 
USING Color_Descriptor 
OPTIONS (
 data_type='image',
 file_type=['.jpg']
 ) 
FROM '/data/thanosAlgo/image_search/junyoung_test/'
```