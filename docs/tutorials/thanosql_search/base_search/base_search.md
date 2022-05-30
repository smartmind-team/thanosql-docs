# SEARCH와 SELECT 쿼리로 데이터 검색하기

**[이전 문서 - ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ](https://github.com/smartmind-team/thanosql-docs/blob/dowon/docs/tutorials/thanosql_ml/image/classification/image_classification_kor_lv1.md)** <br>| **[다음 문서 - ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/classification/automl_classification/)**

### 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간(실행시간): 10분
- 사용언어: [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일:
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개

이번 튜토리얼에서는 SEARCH 구문과 SELECT 구문을 함께 사용하여 어떻게 원하는 데이터를 검색할 수 있는지를 알아보겠습니다.



```sql
%load_ext thanosql
```

## 1. 데이터 세트 확인

이번 예제에서 사용할 데이터를 확인해보겠습니다. 사용할 데이터는 `diet`로, 1190장의 음식 사진으로 된 데이터세트입니다.


```sql
%%thanosql
SELECT * FROM diet
```

![IMAGE](/img/thanosql_search/base_search/select_dataset.png)


```sql
%%thanosql
BUILD MODEL diet_image_classification
USING ConvNeXt_Tiny
OPTIONS (image_col='image', label_col='label', epochs=1)
AS SELECT images ->> 'img_path' image, images ->> 'category' label FROM diet
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

<br>


```sql
%%thanosql
PREDICT USING diet_image_classification
AS SELECT user_id, images ->> 'img_path' image, images ->> 'category' label FROM diet
```

![IMAGE](/img/thanosql_search/base_search/predicted.png)


!!! note "__쿼리 세부 정보__"
    - "__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">diet_image_classification</mark> 모델을 예측에 사용합니다.
    - "__SELECT__" images 열에서 'img_path'를 image로, 'category'를 label로 선택합니다.

<br>

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

![IMAGE](/img/thanosql_search/base_search/predicted_with_where.png)


!!! note "__쿼리 세부 정보__"
    - "__SELECT * FROM (...)__" 쿼리 구문을 통해 PREDICT로 시작하는 쿼리문의 결과를 모두 선택합니다.
    - "__WHERE__" 쿼리 구문을 통해 조건을 설정합니다. 이 조건은 AND를 통해 이어집니다.
        - "label != predicted" : label열과 predicted열이 다른 데이터만 선택합니다.
        - "label LIKE '사과파이'" : label열이 '사과파이'인 데이터만 선택합니다.

<br>

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 `diet` 데이터 세트에서 이미지 예측 결과를 WHERE문으로 선택적으로 고르는 것을 해보았습니다. 이번 튜토리얼은 간단한 예제였지만, ThanoSQL의 비정형 예측과 일반 SQL문법을 결합하면, 훨씬 더 많은 것이 가능할 것입니다.


```sql

```
