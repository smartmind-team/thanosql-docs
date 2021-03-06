
# __영화 평점 데이터를 사용하여 영화 추천 모델 만들기__

**[이전 문서 - Auto-ML을 사용하여 예측 모델 만들기](/tutorials/thanosql_ml/regression/automl_regression/)** <br>
**[다음 문서 - 오디오 파일을 받아쓰는 음성 인식 모델 만들기](/tutorials/thanosql_ml/audio_recognition/audio_recognition_wav2vec/)**

## 시작 전 사전정보
- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/ml/추천시스템 만들기/영화 평점 데이터를 사용하여 영화 추천 모델 만들기.ipynb
- 참고 문서 : [Movielens 데이터 세트](https://grouplens.org/datasets/movielens/), [LightFM](https://making.lyst.com/lightfm/docs/home.html)
- 마지막 수정날짜 : {{ git_revision_date_localized }}

## 튜토리얼 소개

!!! note "__추천 모델 이해하기__"
     일반적으로 추천 모델에서 해당 플랫폼의 특정 사용자는 플랫폼 내의 전체 아이템(제품 또는 콘텐츠)이 아닌 소수의 아이템만을 소비(시청여부, 구매이력, 후기, 평점 등)하게 됩니다. 아직 소비하지 않은 아이템에 대해서 특정 소비자가 어떤 평점을 줄지 예측하고 높은 평점을 줄 것으로 예측되는 아이템(하지만 소비되지 않은 아이템)을 예측 평점이 높은 순위부터 보여줍니다. 

넷플릭스와 같은 [OTT 서비스](https://ko.wikipedia.org/wiki/OTT_%EC%84%9C%EB%B9%84%EC%8A%A4)를 사용하면 'OO님이 좋아할만한 영화', 'OO님이 좋아할만한 드라마' 등의 콘텐츠 추천 리스트들을 볼 수 있습니다. OTT 서비스 외에도 온라인 쇼핑몰, 온라인 뉴스, 온라인 광고 등 온라인 플랫폼 시장에는 사용자를 사로잡기 위한 다양한 추천 모델들이 존재합니다. 이처럼 플랫폼 내에서 사용자의 체류시간 또는 인게이지먼트 등을 높이고 타 플랫폼과의 경쟁력을 높이기 위해 추천 모델의 중요성은 계속 증가하고 있습니다.

ThanoSQL은 추천 모델 알고리즘을 자동화하여 머신러닝이나 데이터 과학(Data Science)에 대한 지식이 없어도 서비스 제공자들이 간단한 쿼리구문만을 이용해 추천 모델을 구축할 수 있도록 도와줍니다.  

__아래는 ThanoSQL (영화) 추천 모델의 활용 및 기대효과 예시입니다.__

- 플랫폼 내에서 사용자에게 항상 정적인 아이템 리스트를 보여주는 것보다, 추천 모델을 활용해 사용자의 방문 또는 구매 이력, 평점, 행동 방식(User Behaviour) 등을 활용해 고객의 특성과 상황에 맞게 동적인 아이템 리스트를 보여줌으로써 매출 증대 효과를 기대할 수 있습니다.    

- 플랫폼 내에서 사용자와 아이템(숙박 시설, 친구 또는 파트너, 제품, 동영상 또는 콘텐츠 등) 간의 매칭에 추천 모델을 활용하여 사용자들의 만족도를 높이고 체류시간이나 인게이지먼트를 향상 시킬 수 있습니다. 

!!! note "본 튜토리얼에서는"
    :point_right: 사용자가 콘텐츠에 부여한 평점 데이터를 사용하여 간단하게 사용자가 좋아할만한 영화 목록을 추천해주는 추천시스템을 구축합니다. 이를 위해, <mark style="background-color:#FFD79C">__Movielens__</mark> 영화 평점 데이터 세트를 사용합니다. <mark style="background-color:#FFD79C">__Movielens__</mark> 데이터 세트는 사용자가 특정 영화에 대해서 평점을 준 것을 모아놓은 데이터 세트입니다. <br>

## __0. 데이터 세트 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스 사용](/quick_start/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.   

```sql
%load_ext thanosql
```
```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```
```sql
%%thanosql
COPY movielens_data 
FROM "tutorial_data/movielens_data/movielens.csv"
```

!!! note "" 
    COPY [테이블 명] FROM [csv 파일]  
    - 위의 쿼리는 csv 파일 데이터 세트를 ThanoSQL DB의 테이블로 만들어 줍니다.



## __1. 데이터 세트 확인__

영화 평점 데이터를 이용한 영화 추천 모델을 만들기 위해 ThanoSQL [DB](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 저장되어 있는 <mark style="background-color:#FFEC92 ">movielens_data</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.

```sql
%%thanosql 
SELECT * 
FROM movielens_data 
LIMIT 5
```
![IMAGE](/img/recommendation_lfm_img1.png)

!!! note "데이터 이해하기"
    <mark style="background-color:#FFEC92 ">movielens_data</mark> 데이터 세트는 다음과 같은 정보를 담고 있습니다.  
    - <mark style="background-color:#D7D0FF ">userid</mark> : 사용자 ID   
    - <mark style="background-color:#D7D0FF ">movieid</mark> : 영화 ID  
    - <mark style="background-color:#D7D0FF ">rating</mark> : 평점   
    - <mark style="background-color:#D7D0FF ">title</mark> : 영화 제목  
    <mark style="background-color:#D7D0FF ">userid</mark> 값이 31인 사용자는 "Toy Story (1995)"에 평점 3.0점을 주고 "Braveheart (1995)"에 평점 5.0점을 준 것을 확인할 수 있습니다. 


## __2. 추천 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">movielens_data</mark> 데이터를 사용하여 영화 추천 모델을 만듭니다. 다음 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD ">my_movie_rec</mark> 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간 : 1 min)

```sql
%%thanosql
BUILD MODEL my_movie_rec
USING Light_FM
OPTIONS (
  user_col='userid',   
  item_col='movieid',
  rating_col='rating',
  description_col='title'
  )
AS 
SELECT * 
FROM movielens_data
```

!!! note "__쿼리 세부 정보__"
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">my_movie_rec</mark> 이라는 모델을 만들고 학습시킵니다. <br>
    - "__OPTIONS__"에서는 3개의 컬럼(Column)이 사용합니다. "user_col"에는 사용자의 ID (<mark style="background-color:#D7D0FF ">userid</mark>), "item_col"에는 아이템의 ID가 들어갑니다. 본 튜토리얼에서는 <mark style="background-color:#D7D0FF ">movieid</mark>가 사용됩니다. "rating_col"에서는 예측하고자 하는 목표값이 되는 열의 이름(<mark style="background-color:#D7D0FF ">rating</mark>)을 적어줍니다.   

!!! tip ""
    "description_col"의 경우 모델의 학습에는 사용되지 않지만 결과값 확인의 편의성을 위해 "item" 컬럼의 설명(<mark style="background-color:#D7D0FF ">title</mark>)을 넣어 줄 수 있습니다. 

## __3. 생성된 모델을 사용하여 추천 목록 생성__

이전 단계에서 생성한 영화 추천 모델을 사용해서 특정 사용자가 좋아할만한 아이템(영화) 목록을 10개 생성합니다.

```sql
%%thanosql
PREDICT USING my_movie_rec
OPTIONS (
  predict_type='user', 
  user_id=31, 
  nrec=10
  )
AS 
SELECT * 
FROM movielens_data
```
![IMAGE](/img/recommendation_lfm_img2.png)

!!! note "__쿼리 세부 정보__" 
    - __"PREDICT USING"__ 쿼리 구문을 사용하여 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">my_movie_rec</mark> 모델을 예측에 사용합니다. 
    - 추천 모델에서는 예측 단계에서도 "__OPTIONS__"를 사용합니다. "predict_type"은 예측 결과를 정렬할 대상 기준을 설정합니다. 이번 튜토리얼에서는 특정 사용자(<mark style="background-color:#D7D0FF ">userid</mark>의 값이 31)에게 추천할 영화 목록을 보려고 하기 때문에 "itemid"를 적어주며 "itemid"는 보고자 하는 특정 사용자의 <mark style="background-color:#D7D0FF ">userid</mark>값인 31을 입력합니다. "nrec"는 추천하는 아이템의 개수를 의미합니다. 

!!! warning
    "__OPTIONS__"와 관련 있는 추가적인 옵션값들은 [중급 추천 시스템 만들기] 튜토리얼에서 다루게 됩니다.


## __4. 튜토리얼을 마치며__ 

이번 튜토리얼에서는 <mark style="background-color:#FFD79C">Movielens</mark> 데이터 세트를 사용하여 영화 평점기반 추천 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. <br>
다음단계인  [중급 추천 모델 만들기] 튜토리얼에서는 추천 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 추천 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요. <br>

* [나만의 데이터 업로드하기](/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 추천 모델 만들기]
* [나만의 추천 분류 모델 배포하기](/how-to_guides/thanosql_api/rest_api_thanosql_query/)

   
!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    추천 모델 구축 관련 문의: contact@smartmind.team
