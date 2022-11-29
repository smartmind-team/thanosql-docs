## __CREATE TABLE 구문__

이 "__CREATE TABLE__" 구문을 사용하여 이미지별 폴더 경로 정보가 포함되어 있는 테이블 없이도 이미지 폴더 경로를 사용하여 수치화 변환이 가능합니다. 
"__CREATE TABLE__" 표현식은 "__FROM__" 뒤에 나오는 이미지 폴더 경로의 이미지 파일들을 수치화하여 테이블로 저장합니다. 
​
``` sql
CREATE TABLE (table_name_expression) 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expr)
``` 
!!!faq ""
    - 본 쿼리를 통해서 USING 뒤에 나온 SimCLR 모델을 사용하여 도출된 수치화 벡터를 CREATE TABLE 뒤에 나온 table_name_expression 이름으로 저장합니다.

​
__OPTIONS 절__
​
```sql
OPTIONS(
    (path_type = {'folder'|'file'}),    
    (data_type = {'image'|'audio'|'video'}),
    (file_type = LIST),
    [overwrite = {True | False}]
    )
```
​
"__OPTIONS__" 는 이미지 수치화를 위한 이미지 파일의 속성값들을 정의합니다. 각 매개변수의 의미는 아래와 같습니다.  

- "path_type" : 데이터가 저장되어 있는 파일 경로의 타입을 설정합니다.(folder|file)

- "data_type" : 입력하는 비정형 데이터의  종류를 설정합니다. (image|audio|video)

- "file_type" : 대상 파일의 확장자를 리스트로 정의하여 줍니다. (ex. ['.jpg'], ['.png'])
​
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)

