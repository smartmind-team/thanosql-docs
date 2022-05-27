# CLIP을 통한 의미 검색

## (ThanoSQL 무작정 따라하기) 텍스트로 원하는 이미지 검색하기

**[이전 문서 - ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ](https://github.com/smartmind-team/thanosql-docs/blob/dowon/docs/tutorials/thanosql_ml/image/classification/image_classification_kor_lv1.md)** <br>| **[다음 문서 - ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/classification/automl_classification/)**

### 시작 전 사전 정보

- 튜토리얼 난이도: ★★☆☆☆
- 읽는데 걸리는 시간(실행시간): 15분
- 사용언어: [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일:
- 참고문서: [Unsplash dataset](https://unsplash.com/data), [openai/CLIP](https://github.com/openai/CLIP)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개

ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ

### 의도 & 제한사항

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

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

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>photo_id</th>
      <th>filepath</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>/data/tutorial/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>/data/tutorial/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>/data/tutorial/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>/data/tutorial/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>/data/tutorial/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
    </tr>
  </tbody>
</table>
</div>

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

![print_image_01](/docs/img/thanosql_search/image_search/print_image_01.png)

## 2. CONVERT를 통해 이미지 임베딩 생성하기

```sql
%%thanosql
CONVERT USING clip_en
OPTIONS(image_col='filepath', table_name='unsplash_data', batch_size=128)
AS SELECT * FROM unsplash_data
```

```sql
%%thanosql
SELECT *
FROM unsplash_data
LIMIT 5
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>photo_id</th>
      <th>filepath</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>clip_vit_embedding</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>/data/tutorial/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.1740911602973938, 0.6694010496139526, 0.19...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>/data/tutorial/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.4623917043209076, 0.41157785058021545, -0....</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>/data/tutorial/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.29828089475631714, 0.6044139862060547, 0.0...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>/data/tutorial/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.011878453195095062, 0.1400068998336792, -0....</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>/data/tutorial/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.3844836950302124, 0.0722995176911354, 0.05...</td>
    </tr>
  </tbody>
</table>
</div>

## 3. 텍스트로 이미지 검색하기

임베딩을 생성했으니, 이제 SEARCH 쿼리를 통해 이미지를 검색할 수 있습니다. 우선 하나의 텍스트로 검색을 해보겠습니다.

```sql
%%thanosql
SEARCH IMAGE text='a black cat'
USING clip_en
AS SELECT * FROM unsplash_data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>photo_id</th>
      <th>filepath</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>clip_vit_embedding</th>
      <th>clip_en_CLIPEn_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>/data/tutorial/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.1740911602973938, 0.6694010496139526, 0.19...</td>
      <td>0.181038</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>/data/tutorial/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.4623917043209076, 0.41157785058021545, -0....</td>
      <td>0.144811</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>/data/tutorial/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.29828089475631714, 0.6044139862060547, 0.0...</td>
      <td>0.179360</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>/data/tutorial/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.011878453195095062, 0.1400068998336792, -0....</td>
      <td>0.178153</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>/data/tutorial/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.3844836950302124, 0.0722995176911354, 0.05...</td>
      <td>0.201175</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24963</th>
      <td>c7OrOMxrurA</td>
      <td>/data/tutorial/unsplash_data/c7OrOMxrurA.jpg</td>
      <td>https://images.unsplash.com/photo-159300793778...</td>
      <td>None</td>
      <td>black metal fence during daytime</td>
      <td>[-0.24536675214767456, -0.11746655404567719, -...</td>
      <td>0.214148</td>
    </tr>
    <tr>
      <th>24964</th>
      <td>15IuQ5a0Qwg</td>
      <td>/data/tutorial/unsplash_data/15IuQ5a0Qwg.jpg</td>
      <td>https://images.unsplash.com/photo-159296761254...</td>
      <td>Pearl earrings and seashells</td>
      <td>white and brown seashell on white surface</td>
      <td>[-0.39285042881965637, 0.5065885186195374, 0.1...</td>
      <td>0.141949</td>
    </tr>
    <tr>
      <th>24965</th>
      <td>w8nrcXz8pwk</td>
      <td>/data/tutorial/unsplash_data/w8nrcXz8pwk.jpg</td>
      <td>https://images.unsplash.com/photo-159299937329...</td>
      <td>None</td>
      <td>leopard on brown tree trunk during daytime</td>
      <td>[0.08376545459032059, -0.33006206154823303, -0...</td>
      <td>0.215514</td>
    </tr>
    <tr>
      <th>24966</th>
      <td>n1jHrRhehUI</td>
      <td>/data/tutorial/unsplash_data/n1jHrRhehUI.jpg</td>
      <td>https://images.unsplash.com/photo-159192792878...</td>
      <td>Floral truck in the streets of Rome</td>
      <td>woman in beige coat and white hat standing on ...</td>
      <td>[0.06461465358734131, -0.015747621655464172, 0...</td>
      <td>0.167231</td>
    </tr>
    <tr>
      <th>24967</th>
      <td>Ic74ACoaAX0</td>
      <td>/data/tutorial/unsplash_data/Ic74ACoaAX0.jpg</td>
      <td>https://images.unsplash.com/photo-159240763188...</td>
      <td>None</td>
      <td>green plants on brown rocky mountain under blu...</td>
      <td>[-0.26213258504867554, 0.6805394887924194, -0....</td>
      <td>0.149835</td>
    </tr>
  </tbody>
</table>
<p>24968 rows × 7 columns</p>
</div>

!!! note "쿼리 세부 정보"
    - "__SEARCH IMAGE__" 쿼리 구문을 사용하여 이미지를 찾을 것임을 명시합니다.
        - "text" : 이 텍스트와 유사한 이미지를 찾습니다.
    - "__USING__" 쿼리 구문을 통해 검색에 사용할 모델로 'CLIPEn'을 사용할 것을 명시합니다.

쿼리의 결과로 `clip_en_CLIPEn_similarity1` 행이 생성된 것을 볼 수 있습니다. 그러나 이 상태로는 해당 행이 어떤 의미가 있는 지 짐작하기 어렵습니다. 조금 더 의미있는 정보를 얻기 위해 몇가지 쿼리를 같이 사용해봅시다.

```sql
%%thanosql
SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
    (SEARCH IMAGE text='a black cat'
    USING clip_en
    AS SELECT * FROM unsplash_data)
ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image</th>
      <th>clip_en_CLIPEn_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>/data/tutorial/unsplash_data/UMyfDjQ6Ep8.jpg</td>
      <td>0.314607</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/tutorial/unsplash_data/3LPGWASiSbM.jpg</td>
      <td>0.313257</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/tutorial/unsplash_data/m8HsSWh-y6E.jpg</td>
      <td>0.309940</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/tutorial/unsplash_data/aFyD5aWKu6k.jpg</td>
      <td>0.307605</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/tutorial/unsplash_data/6ST6S6i9IGM.jpg</td>
      <td>0.306069</td>
    </tr>
  </tbody>
</table>
</div>

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
    (SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
        (SEARCH IMAGE text='a black cat'
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5)
```

![a black cat](/docs/img/thanosql_search/image_search/search_result_black_cat.png)

!!! note "쿼리 세부 정보"
    이 쿼리는 위의 쿼리와 합쳐 세 단계로 구성됩니다.
    - 첫 번째 괄호 안의 "__SELECT__" 쿼리 구문을 통해 바로 위 단계의 결과를 생성합니다.
    - "__PRINT IMAGE__" 쿼리 구문을 사용하여 해당 이미지를 출력합니다.

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
        (SEARCH IMAGE text='a dog on a chair'
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5)
```

![a dog on a chair](/docs/img/thanosql_search/image_search/search_result_dog_on_chair.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
        (SEARCH IMAGE text='sunset on beach'
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5)
```

![sunset on beach](/docs/img/thanosql_search/image_search/search_result_sunset_on_beach.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
        (SEARCH IMAGE text='gloomy photos'
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5)
```

![gloomy photos](/docs/img/thanosql_search/image_search/search_result_gloomy_photos.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
        (SEARCH IMAGE text='programming language python'
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5)
```

![programming language python](/docs/img/thanosql_search/image_search/search_result_python.png)

```sql
%%thanosql
PRINT IMAGE AS
    (SELECT filepath as image, clip_en_CLIPEn_similarity1 FROM
        (SEARCH IMAGE text='the feeling when your program finally works'
        USING clip_en
        AS SELECT * FROM unsplash_data)
    ORDER BY clip_en_CLIPEn_similarity1 DESC LIMIT 5)
```

![finally works](/docs/img/thanosql_search/image_search/search_result_finally_works.png)

## 5. 튜토리얼을 마치며

이번 튜토리얼에서는 CLIP 모델을 사용하여 unsplash 데이터 세트에서 텍스트를 통한 이미지 검색을 해보았습니다.
