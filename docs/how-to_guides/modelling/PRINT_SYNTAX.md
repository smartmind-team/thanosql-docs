# __결과 출력하기 (PRINT)__

**[이전 문서 - 비정형 데이터 검색하기](/how-to_guides/modelling/SEARCH_SYNTAX/)**  
**[다음 문서 - ThanoSQL 알고리즘 쿼리 구문](/how-to_guides/modelling/OPTIONS/)**

## __1. PRINT 구문 개요__

사용자는 "__PRINT__" 구문을 사용하여 이미지, 음성 그리고 비디오 파일을 출력할 수 있습니다. 또한, 서브 쿼리를 사용하여 SEARCH 구문을 통해 나온 결과를 바로 출력 할 수 있습니다. 

## __2. PRINT 구문__
"__PRINT__" 구문
```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
AS 
[출력할 데이터 세트]
```
서브쿼리 사용한 "__PRINT__" 구문
```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
OPTIONS( 
    base_dir = [주피터 서버 루트 경로],
    image_column | audio_column | video_column = [이미지 경로 열 이름]) 
AS 
[출력할 데이터 세트]
```
## __3. PRINT 구문 예시__

### __3.1 이미지 출력__ 

PRINT 쿼리문을 사용하여 데이터 테이블에 있는 이미지 파일들을 출력합니다.
```sql
%%thanosql
PRINT IMAGE 
OPTIONS (
    base_dir='/',
    image_column='image' 
    )
AS 
SELECT * 
FROM junyong_img 
```

!!! note ""
    - `junyong_img` : 이미지 파일 경로가 저장되어 있는 데이터 테이블

### __3.2 음성 출력__

PRINT 쿼리문을 사용하여 데이터 테이블에 있는 음성 파일들을 출력합니다.

```sql
%%thanosql
PRINT AUDIO
OPTIONS (
    base_dir='/',
    audio_column='audio' 
    )
AS 
SELECT * 
FROM junyong_aud
```
![imaeg](/img/PRINT_img1.png) <br>

!!! note ""
    - `junyong_aud` : 음성 파일 경로가 저장되어 있는 데이터 테이블


### __3.3 영상 출력__

PRINT 쿼리문을 사용하여 데이터 테이블에 있는 영상 파일들을 출력합니다.

```sql
%%thanosql
PRINT VIDEO
OPTIONS (
    base_dir='/',
    video_column='video' 
    )
AS 
SELECT * 
FROM junyong_vid
```
!!! note ""
    - `junyong_vid` : 영상 파일 경로가 저장되어 있는 데이터 테이블

### __3.4 서브 쿼리를 사용하여 출력하기__

다음 쿼리는 이전 [SEARCH쿼리](../modelling/SEARCH_SYNTAX.md)에서 만들었던 SEARCH 쿼리문을 PRINT 구문의 서브 쿼리로 사용하여 SEARCH의 결과 테이블을 바로 출력합니다.

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT img_path as image, query1_score 
    FROM (
        SEARCH IMAGE text='12345'
        USING clip_en
        AS 
        SELECT * 
        FROM mnist_dataset)
    ORDER BY query1_score DESC 
    LIMIT 10)
```