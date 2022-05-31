# __CLIP을 통한 의미 검색__

## (ThanoSQL 무작정 따라하기) 텍스트로 원하는 이미지 검색하기

**[이전 문서 - 자기주도학습 임베딩 추출모델을 사용하여 MNIST 손글씨 이미지 분류하기](/tutorials/thanosql_search/image_search/simclr_image_search/)** <br> **[다음 문서 - Auto-ML을 사용하여 타이타닉 생존자 분류 모델 만들기](/tutorials/thanosql_ml/classification/automl_classification/)**

### 시작 전 사전 정보

- 튜토리얼 난이도: ★★☆☆☆
- 읽는데 걸리는 시간(실행시간): 15분
- 사용언어: [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 참고문서: [Unsplash dataset](https://unsplash.com/data), [openai/CLIP](https://github.com/openai/CLIP)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개

>__이미지 임베딩 이해하기__ : 텍스트 시스템과 달리, 이미지 처리 시스템은 개별적인 원시 픽셀 강도를 지닌 이미지를 나타내는 풍부하고 고차원적인 데이터세트로 작동합니다. 하지만 원래의 밀도가 높은 형태의 이미지는 일부 작업에 그리 유용하지 않을 수 있습니다. 예를 들어 잡지 표지 이미지를 보고 비슷한 잡지를 찾거나 참조 사진과 비슷한 사진을 찾아야 한다고 가정해보세요. 입력 사진의 원시 픽셀(2,048 ✕ 2,048)을 다른 사진과 비교하여 비슷한지 여부를 찾는 것은 효율적이거나 효과적이지 않습니다. 하지만 이미지의 저차원적 특성 벡터(임베딩)를 추출하면 이미지에 포함된 내용이 무엇인지를 나타내는 일정한 지표를 얻고, 더 효과적으로 비교할 수 있습니다.

>__CLIP 이해하기__ : openai의 CLIP 이전의 이미지 모델들은 미리 정의된 카테고리의 데이터에 대해 예측하도록 훈련되었습니다. 이런 지도학습 방식의 모델은 그 사용성에 한계가 있습니다. CLIP은 이미지-라벨 쌍으로부터 훈련된 것이 아닌, 웹상에서 수집한 4억개의 이미지-캡션 데이터로 학습하여, 이미지와 텍스트가 서로의 임베딩을 공유하도록 한 것이 특징입니다.

<br>

이번 튜토리얼에서는 CLIP모델을 사용하여, 텍스트로 Unsplash 데이터세트의 25,000장의 이미지 중에서 원하는 이미지를 찾는 것을 해볼 것입니다.

## 1. 데이터 세트 확인

이번 예제에서 사용할 데이터를 확인해보겠습니다. 사용할 데이터는 `Unsplash dataset - lite`로, 25000장의 이미지와 관련된 메타데이터로 이루어진 데이터 세트입니다. 이 튜토리얼에서는 이미지만을 사용할 예정입니다. 아래 SQL문을 통해 데이터를 확인할 수 있습니다.

<br>

```sql
%load_ext thanosql
```

```sql
%%thanosql
SELECT photo_id, filepath, photo_image_url, photo_description, ai_description
FROM unsplash_data
LIMIT 5
```

![IMAGE](/img/thanosql_search/image_search/select_data.png)

> #### 데이터 이해하기
>
> - `photo_id` 이미지의 고유 id
> - `filepath` 이미지가 위치한 경로
> - `photo_image_url` 웹사이트 unsplash에서의 원본 이미지 주소
> - `photo_description` 해당 이미지에 대해 사람이 작성한 짧은 설명
> - `ai_description` AI가 생성해낸 해당 이미지에 대한 설명

```sql
%%thanosql
PRINT IMAGE AS
SELECT filepath as image FROM unsplash_data LIMIT 5
```

![IMAGE](/img/thanosql_search/image_search/print_dataset_image.png)

## 2. CONVERT를 통해 이미지 임베딩 생성하기

```sql
%%thanosql
CONVERT USING clip_en
OPTIONS(image_col="filepath", table_name="unsplash_data", batch_size=128)
AS SELECT * FROM unsplash_data
```

```sql
%%thanosql
SELECT *
FROM unsplash_data
LIMIT 5
```

![IMAGE](/img/thanosql_search/image_search/select_data_with_embedding.png)

## 3. 텍스트로 이미지 검색하기

임베딩을 생성했으니, 이제 SEARCH 쿼리를 통해 이미지를 검색할 수 있습니다. 우선 하나의 텍스트로 검색을 해보겠습니다.

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING clip_en
AS SELECT * FROM unsplash_data
```

![IMAGE](/img/thanosql_search/image_search/search_result_raw.png)

!!! note "쿼리 세부 정보"
    - "__SEARCH IMAGE__" 쿼리 구문을 사용하여 이미지를 찾을 것임을 명시합니다.
        - "text" : 이 텍스트와 유사한 이미지를 찾습니다.
    - "__USING__" 쿼리 구문을 통해 검색에 사용할 모델로 'CLIPEn'을 사용할 것을 명시합니다.

쿼리의 결과로 `clip_en_CLIPEn_similarity1` 행이 생성된 것을 볼 수 있습니다. 그러나 이 상태로는 해당 행이 어떤 의미가 있는 지 짐작하기 어렵습니다. 조금 더 의미있는 정보를 얻기 위해 몇가지 쿼리를 같이 사용해봅시다.

```sql
%%thanosql
SELECT filepath as image, "clip_en_CLIPEn_similarity1" FROM
    (SEARCH IMAGE text="a black cat"
    USING clip_en
    AS SELECT * FROM unsplash_data)
ORDER BY "clip_en_CLIPEn_similarity1" DESC LIMIT 5
```

![IMAGE](/img/thanosql_search/image_search/search_result_sorted.png)

!!! note "쿼리 세부 정보"
    이 쿼리는 두 단계로 구성됩니다.
    - 괄호 안의 "__SEARCH IMAGE__"로 시작하는 쿼리 구문은 바로 위 단계의 결과를 반환합니다.
    - 괄호 밖의 "__SELECT__" 쿼리 구문은 괄호 안의 쿼리 결과에서 filepath열과 clip_en_CLIPEn_similarity1열을 선택합니다. 이 때, filepath열은 image라는 이름으로 받습니다.
    - "__ORDER BY__" 쿼리 구문은 결과를 clip_en_CLIPEn_similarity1열의 값을 기준으로 정렬하는데, 정렬은 내림차순(DESC)이며, 그 중 상위 5개만을(LIMIT 5) 나타냅니다.


이제 이미지가 'a black cat'과 가장 유사한 순서대로 정렬되어 보여집니다.

이 쿼리 앞에 PRINT문을 같이 사용한다면, 결과 이미지를 바로 확인할 수 있습니다.

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, "clip_en_CLIPEn_similarity1" FROM
        (SEARCH IMAGE text="a black cat"
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY "clip_en_CLIPEn_similarity1" DESC LIMIT 5)
```

![a black cat](/img/thanosql_search/image_search/result_black_cat.png)

!!! note "쿼리 세부 정보"
    이 쿼리는 위의 쿼리와 합쳐 세 단계로 구성됩니다.
    - 첫 번째 괄호 안의 "__SELECT__" 쿼리 구문을 통해 바로 위 단계의 결과를 생성합니다.
    - "__PRINT IMAGE__" 쿼리 구문을 사용하여 해당 이미지를 출력합니다.

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, "clip_en_CLIPEn_similarity1" FROM
        (SEARCH IMAGE text="a dog on a chair"
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY "clip_en_CLIPEn_similarity1" DESC LIMIT 5)
```

![a dog on a chair](/img/thanosql_search/image_search/result_dog_on_chair.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, "clip_en_CLIPEn_similarity1" FROM
        (SEARCH IMAGE text="gloomy photos"
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY "clip_en_CLIPEn_similarity1" DESC LIMIT 5)
```

![gloomy photos](/img/thanosql_search/image_search/result_gloomy.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, "clip_en_CLIPEn_similarity1" FROM
        (SEARCH IMAGE text="programming language python"
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY "clip_en_CLIPEn_similarity1" DESC LIMIT 5)
```

![programming language python](/img/thanosql_search/image_search/result_python.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, "clip_en_CLIPEn_similarity1" FROM
        (SEARCH IMAGE text="the feeling when your program finally works"
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY "clip_en_CLIPEn_similarity1" DESC LIMIT 5)
```

![finally works](/img/thanosql_search/image_search/result_happy.png)

## 5. 튜토리얼을 마치며

이번 튜토리얼에서는 CLIP 모델을 사용하여 unsplash 데이터 세트에서 텍스트를 통한 이미지 검색을 해보았습니다. 초급 단계의 튜토리얼인만큼 간단한 쿼리를 통해 눈에 보이는 결과를 얻는 것 위주로 진행했습니다. 이미지 검색을 조금 더 다채로운 쿼리와 함께 사용한다면, 보다 원하는 결과에 가까운 값을 얻을 수 있을 것입니다.


!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    이미지 분류 모델 구축 관련 문의: contact@smartmind.team