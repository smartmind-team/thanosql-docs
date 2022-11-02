---
title: PREPROCESS
---

# __PREPROCESS__

## __1. PREPROCESS 문__

사용자는 "__PREPROCESS__" 문을 사용하여 본인의 데이터(텍스트, 이미지, 오디오, 비디오)를 제공되는 전처리 방법으로 가공하고 ThanoSQL DB 안의 데이터 테이블로 생성합니다.

## __2. PREPROCESS 구문__

```sql
%%thanosql
PREPROCESS TEXT | IMAGE | AUDIO | VIDEO
USING [데이터 전처리 방법]
OPTIONS ([전처리 방법에 필요한 옵션])
FROM
[워크스페이스에 있는 데이터 파일의 절대 경로]
```

!!! note "__쿼리 세부 정보__"    
    - "__OPTIONS__" 쿼리 구문을 통해 __PREPROCESS__ 에 사용할 옵션을 지정합니다.  
        - "interval" : 비디오 분할 주기 (DEFAULT : 10)
        - "table_name" : ThanoSQL DB에 생성될 데이터 테이블명
        - "column_name" : 전처리된 테이터가 저장될 컬럼명
        - "folder_name" : 전처리된 파일 데이터가 저장될 폴더명

## __3. PREPROCESS 예시__

아래 예는 비디오 전처리 방법인 'split(분할)'을 사용하여 mp4 파일을 전처리하고 데이터 테이블을 생성합니다. 

```sql
%%thanosql
PREPROCESS VIDEO
USING split
OPTIONS (
    interval=20,
    table_name='video_table',
    column_name='video_path',
    folder_name='video_splitted'
    )
FROM  
"video_files/video_example.mp4"
```
