
# __Microsoft 뉴스 데이터 세트를 사용하여 뉴스 추천 모델 만들기__

**[이전 문서 - Movielens 영화 평점 데이터 세트를 사용하여 영화 추천 모델 만들기](/tutorials/thanosql_ml/recommendation/recommendation_lfm/)** <br> ** [다음 문서 - 오디오 파일을 받아쓰는 음성 인식 모델 만들기](/tutorials/thanosql_ml/audio_recognition/audio_recognition_wav2vec/)**

## 시작 전 사전정보

* 튜토리얼 난이도 : ★☆☆☆☆
* 읽는 시간 : 7분
* 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
* 실행 예제 파일 : [News_Recommender.ipynb](http://35.222.17.152:8888/lab/workspaces/auto-t/tree/indoo/techdoc/lda/lda_kor_lv1.ipynb)
* 참고 문서 : [Latent Dirichlet Allocation 공식 논문](https://www.jmlr.org/papers/volume3/blei03a/blei03a.pdf), [Gensim Github](https://github.com/RaRe-Technologies/gensim)
* 마지막 수정날짜 : 2022-06-01


## 튜토리얼 소개 

!!! note "__추천 모델 이해하기__"
    일반적으로 추천 모델에서 해당 플랫폼의 특정 사용자는 플랫폼 내의 전체 아이템(제품 또는 콘텐츠)이 아닌 소수의 아이템만을 소비(시청여부, 구매이력, 후기, 평점 등)하게 됩니다. 아직 소비하지 않은 아이템에 대해서 특정 소비자가 어떤 평점을 줄지 예측하고 높은 평점을 줄 것으로 예측되는 아이템(하지만 소비되지 않은 아이템)을 예측 평점이 높은 순위부터 보여줍니다. 

한 조사에 따르면 10명 중 7명 이상은 네이버 또는 다음 등의 포털 사이트에서 뉴스를 보는데, 포털 사이트들은 자체 추천 알고리즘을 통해 사용자에게 맞춤형 뉴스를 제공합니다. 방대한 정보 속에서 "결정피로"를 줄이고 최소 비용으로 최대의 만족을 얻기 위해 사용자들은 추천 아이템을 소비하고 있습니다. 이에 플랫폼 내에서 사용자의 체류시간이나 인게이지먼트 등을 높이고 타 플랫폼과의 경쟁력을 높이기 위해 추천 모델의 중요성은 계속 증가하고 있습니다. 

ThanoSQL은 추천 모델 알고리즘을 자동화하여 머신러닝이나 데이터 과학(Data Science)에 대한 지식이 없어도 서비스 제공자들이 간단한 쿼리구문만을 이용해 추천 모델을 구축할 수 있도록 도와줍니다. <br>

__아래는 ThanoSQL (뉴스) 추천 모델의 활용 및 기대효과 예시입니다.__

* 온라인서점 플랫폼에서 도서 별 요약 내용을 바탕으로 고객이 좋아할만한 도서를 추천 해줄 수 있습니다. 단순하게 주문량과 평점을 통한 추천이 아닌 실제 내용을 바탕으로 유저의 취향에 맞는 도서를 추천하여 고객의 만족도를 높이고 매출증대 효과를 기대할 수 있습니다.

* 실시간으로 수만개의 글이 올라오는 온라인 커뮤니티 사이트에서 추천 모델을 적용하여 사용자 별 좋아하는 글이나 콘텐츠를 보여줄 수 있습니다. 사용자는 이를 통해 수많은 게시물 중에서 자신이 관심 있어하는 아이템을 소비할 수 있어 사용자들의 만족도를 높이고 체류시간이나 인게이지먼트를 향상 시킬 수 있습니다.

!!! note "본 튜토리얼에서는" 
    :point_right: 사용자가 소비한 아이템(뉴스)의 내용(제목, 카테고리, 키워드 등)에 관한 데이터를 사용하여 간단한 추천 모델을 구축합니다. 이를 위해, <mark style="background-color:#FFD79C">__Microsoft News __</mark> 데이터 세트를 사용합니다. <mark style="background-color:#FFD79C">__Microsoft News __</mark> 데이터 세트는 대표적인 뉴스 데이터 세트로, MSN에 있는 뉴스 기사들을 모아 놓은 데이터 세트입니다.
    

!!! tip ""
    아이템의 내용이 아닌 사용자가 준 평점 데이터를 사용하여 추천 모델을 만들고 싶다면 다음 "[Movielens 영화평점 데이터를 이용한 영화 추천 모델 만들기](https://github.com/smartmind-team/thanosql-docs/edit/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md)" 튜토리얼을 참조하세요.

## __1. 데이터 세트 확인__

뉴스 추천 모델을 만들기 위해 ThanoSQL [데이터 베이스(DB)](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 저장되어 있는  `mind_train_user_history`, `news_train` 두 개의 테이블을 사용합니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.

```sql
%%thanosql 
SELECT * 
FROM news_train 
LIMIT 5
```

![IMAGE](/img/recommendation_lda_img2.png)

!!! note "데이터 이해하기"
    <mark style="background-color:#FFEC92 ">news_train</mark> 데이터 세트는 사용자가 소비한 뉴스 이력 정보를 담고 있습니다.  
    -  <mark style="background-color:#D7D0FF ">user_id</mark>: 사용자 ID  
    -  <mark style="background-color:#D7D0FF ">news_id</mark>: 사용자가 본 뉴스 ID  
    -  <mark style="background-color:#D7D0FF ">ord</mark> : 뉴스를 본 순서 

```sql
%%thanosql 
SELECT * 
FROM mind_train_user_history 
LIMIT 5
```

![IMAGE](/img/recommendation_lda_img1.png)

!!! note "데이터 이해하기"
    <mark style="background-color:#FFEC92 ">mind_train_user_history</mark> 데이터 세트는 뉴스의 아이디, 범주, 하위범주, 제목, 요약내용 등 뉴스에 대한 추가 정보를 담고 있습니다. 

!!! tip ""
    이번 튜토리얼에서는 뉴스의 아이디(<mark style="background-color:#D7D0FF">id</mark>), 제목( <mark style="background-color:#D7D0FF ">title</mark>), 요약 내용( <mark style="background-color:#D7D0FF ">abstract</mark>) 칼럼 만을 사용하여 모델을 만듭니다. 

## __2. 추천 모델 생성__
이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">news_train</mark> 샘플 데이터를 사용하여 개인화 뉴스 추천 모델을 만듭니다. 다음 쿼리 구문을 실행시켜 <mark style="background-color:#E9D7FD">news_rec</mark> 라는 모델을 만듭니다. 

```sql
%%thanosql
BUILD MODEL news_rec 
USING LDAREC
OPTIONS(
  article_id ='id', 
  article_title='title',
  article_abstract='abstract', 
  user_history_tb_name='mind_train_user_history'
  )
AS 
SELECT * 
FROM news_train
```

!!! note "__쿼리 세부정보__" 
    "__BUILD MODEL__" 쿼리구문을 사용하여 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">news_rec</mark> 이라는 모델을 예측에 사용합니다. 추천 모델에서 "__OPTIONS__"는 4개의 컬럼(Column)이 사용됩니다. "article_id"에는 뉴스의 ID (<mark style="background-color:#D7D0FF ">id</mark>), "article_title"에는 뉴스의 제목 컬럼명(<mark style="background-color:#D7D0FF ">title</mark>), "article_abstract"에는 뉴스의 요약내용 컬럼명(<mark style="background-color:#D7D0FF ">abstract</mark>), "user_history_tb_name"은 사용자의 행동 이력(뉴스를 본 순서)에 대한 테이블명(<mark style="background-color:#FFEC92">mind_train_user_history</mark>)을 적어줍니다. <br>

!!! tip ""
    <mark style="background-color:#FFEC92 ">mind_train_user_history</mark> 테이블은 컬럼명 <mark style="background-color:#D7D0FF ">user_id</mark>, <mark style="background-color:#D7D0FF ">news_id</mark>, <mark style="background-color:#D7D0FF ">ord</mark>를 사용합니다.
 
## __3. 생성된 모델 사용하여 맞춤 뉴스 추천 목록 생성__
이전 단계에서 생성한 개인화 추천 모델을 사용해서 특정 사용자가 좋아할만한 뉴스를 예측해 추천 목록을 만듭니다. 뉴스 추천 결과 사용자 ID가 U9999인 사용자는 주로 정치나 경제 관련 뉴스에 대해 관심이 많은 것을 확인 할 수 있습니다.  

```sql
%%thanosql
PREDICT USING news_rec
OPTIONS(
  user_id = 'U9999', 
  nrec= 5
  ) 
AS 
SELECT * 
FROM news_train
```

![IMAGE](/img/recommendation_lda_img3.png)

!!! note "__쿼리 세부정보__"
    "__PREDICT USING__" 쿼리 구문을 사용하여 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">news_rec</mark> 모델을 예측에 사용합니다. 추천모델에서는 예측 단계에서도 "__OPTIONS__"를 사용합니다. 이번 튜토리얼에서는 특정 사용자("user_id"의 값이 U9999)에게 추천할 뉴스 목록을 보려고 하기 때문에 "user_id"는 보고자 하는 특정 사용자의 <mark style="background-color:#D7D0FF ">user_id</mark>(테이블 <mark style="background-color:#FFEC92 ">mind_train_user_history</mark>) /<mark style="background-color:#D7D0FF ">id</mark>(테이블 <mark style="background-color:#FFEC92 ">news_train</mark>) 값인 U9999 입력합니다. "nrec"는 추천하는 아이템의 개수를 의미합니다.

<br>

## __4.튜토리얼을 마치며__

이번 튜토리얼에서는 <mark style="background-color:#FFD79C">Microsoft news</mark> 데이터 세트를 사용하여 뉴스 추천 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 추천 모델은 각 플랫폼이나 서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있고 평점기반 추천 모델 등과 함께 사용하여 성능을 더욱 높일 수 있습니다. 뉴스 추천 모델은 뉴스 뿐만이 아니라 문서로 된 아이템만 있다면 다양한 분야에서 적용이 가능합니다. 온라인 서점에 적용하여 유저에게 도서 추천, 커뮤니티 사이트의 글 추천 등 뉴스 추천 모델을 통해 나만의 경쟁력있는 플랫폼을 완성 할 수 있습니다. <br>  
다음단계인  [중급 추천 모델 만들기](comingsoon) 튜토리얼에서는 추천 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 추천 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요. 

* [나만의 데이터 업로드하기](comingsoon)  
* [중급 추천 모델 만들기](comingsoon)   
* [나만의 추천 모델 배포하기](comingsoon)  
  

!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__" 
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    추천 모델 구축 관련 문의: contact@smartmind.team