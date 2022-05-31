# __키워드로 이미지 검색하기__ 

**[이전 문서 - 안내 가이드](/quick_start/algorithm_list/)** <br>**[다음 문서 - 자기주도학습 임베딩 추출모델을 사용하여 MNIST 손글씨 이미지 분류하기](/tutorials/thanosql_search/image_search/simclr_image_search/)**

## 시작 전 사전 정보 

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는 시간: 10분
- 사용 언어: [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 참고 문서: [AI 양재 허브 음식 이미지 데이터 세트](https://aihub.or.kr/aidata/30747)
- 마지막 수정날짜 : 2022-06-01

## 튜토리얼 소개 

검색은 "^^책이나 컴퓨터에서, 목적에 따라 필요한 자료들을 찾아냄^^"이라는 사전적인 의미를 가지고 있습니다. 단어가 포함되어 있는지를 기반으로 데이터 베이스 내의 정보를 검색하는 방식과 다르게 ThanoSQL의 이미지 검색은 이미지 특징을 학습해, 검색의 대상과 가장 유사한 정보를 제공해줍니다. 

!!! note "본 튜토리얼에서는" 
    :point_right: ThanoSQL에서 제공하는 비정형 데이터 검색 구문인 SEARCH 쿼리 구문과 기존 PostgreSQL에서 제공하는 정형 데이터 검색 구문인 SELECT 쿼리 구문을 같이 사용하여 원하는 데이터 검색을 진행합니다. 

## __0.ThanoSQL 접속하기__ 

```sql
%load_ext thanosql
```

## __1.데이터 세트 확인__

이번 튜토리얼 진행을 위해 사용할 데이터 세트를 확인합니다. 사용할 데이터 세트는 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92 ">diet</mark>로, 1190장의 음식 사진으로 된 데이터 세트입니다. 아래의 쿼리 구문을 실행하면서 테이블 내용을 확인합니다. 

!!! note ""
    <mark style="background-color:#FFEC92 ">diet</mark>에 저장되어 있는 이미지는 과학기술정보통신부가 주관하고 한국지능정보사회진흥원이 지원하는 '인공지능 학습용 데이터 구축사업'으로 구축된 데이터로 한국인 다빈도 섭취 외식 메뉴와 한식메뉴 400종을 선정하여 양질의 이미지 데이터로 구성이 되어 있습니다. 

```sql
%%thanosql
SELECT * FROM diet
```

![IMAGE](/img/thanosql_search/base_search/select_img1.png)

## __2.검색을 위한 이미지 분류 모델 생성__ 

이미지 검색을 위해서는 기존 데이터 테이블을 학습하여 추후 검색의 기준을 만들어줘야 합니다. 이를 위해서 이전 단계에서 확인한 데이터 세트를 사용하여 이미지 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여  <mark style="background-color:#E9D7FD ">diet_image_classification</mark>이라는 이름의 모델을 만듭니다. 

```sql
%%thanosql
BUILD MODEL diet_image_classification
USING ConvNeXt_Tiny
OPTIONS (
    image_col='image', 
    label_col='label', 
    epochs=1)
AS 
SELECT images ->> 'img_path' image, images ->> 'category' label 
FROM diet
```

    Success

!!! note "쿼리 세부 정보"
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">diet_image_classification</mark> 모델을 만들고 학습시킵니다.
    - "__USING__" 쿼리 구문을 통해 베이스 모델로 `ConvNeXt_Tiny`를 사용할 것을 명시합니다.
    - "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        - "image_col" : 이미지 경로를 담은 열의 이름
        - "label_col" : 목표값의 정보를 담은 열의 이름
        - "epochs : 모든 학습 데이터 세트를 학습하는 횟수
    - "__SELECT__" images 열에서 'img_path'를 image로, 'category'를 label로 선택합니다.


## __3.생성된 모델을 사용하여 분류 결과 예측__

이전 단계에서 만든 이미지 예측 모델(<mark style="background-color:#FFEC92 ">diet_image_classification</mark>)을 사용해서 특정 이미지(<mark style="background-color:#D7D0FF">diet</mark>)의 목표값을 예측해 봅니다. 아래 쿼리를 수행하고 나면, 예측 결과는 <mark style="background-color:#D7D0FF">predicted</mark> 열에 저장되어 반환됩니다.

```sql
%%thanosql
PREDICT USING diet_image_classification
AS SELECT user_id, images ->> 'img_path' image, images ->> 'category' label FROM diet
```

![IMAGE](/img/thanosql_search/base_search/select_img2.png)


!!! note "__쿼리 세부 정보__"
    - "__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">diet_image_classification</mark> 모델을 예측에 사용합니다.
    - "__SELECT__" images 열에서 'img_path'를 image로, 'category'를 label로 선택합니다.

## __4.생성된 모델을 이용항 검색__ 

이제 PREDICT와 SELECT 그리고 WHERE를 같이 사용하여 특정 조건의 데이터만 검색을 해보겠습니다. label이 사과파이이고, 잘못 예측된 데이터만을 보고싶다면 다음처럼 쿼리를 사용할 수 있습니다.


```sql
%%thanosql
SELECT * FROM
    (PREDICT USING diet_image_classification
    AS SELECT user_id, images ->> 'img_path' image, images ->> 'category' label FROM diet)
WHERE label != predicted
AND label LIKE '사과파이'
LIMIT 10
```

![IMAGE](/img/thanosql_search/base_search/select_img3.png)


!!! note "__쿼리 세부 정보__"
    - "__SELECT * FROM (...)__" 쿼리 구문을 통해 PREDICT로 시작하는 쿼리문의 결과를 모두 선택합니다.
    - "__WHERE__" 쿼리 구문을 통해 조건을 설정합니다. 이 조건은 AND를 통해 이어집니다.
        - "label != predicted" : label열과 predicted열이 다른 데이터만 선택합니다.
        - "label LIKE '사과파이'" : label열이 '사과파이'인 데이터만 선택합니다.

<br>

## __5.튜토리얼을 마치며__

이번 튜토리얼에서는 `diet` 데이터 세트에서 이미지 예측 결과를 WHERE문으로 선택적으로 고르는 것을 해보았습니다. 이번 튜토리얼은 간단한 예제였지만, ThanoSQL의 비정형 예측과 일반 SQL문법을 결합하면, 훨씬 더 많은 것이 가능할 것입니다.

