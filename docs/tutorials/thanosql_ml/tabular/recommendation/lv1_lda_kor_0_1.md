# 추천모델 만들기 (2)
## (ThanoSQL 무작정 따라하기) Microsoft 뉴스 데이터세트를 이용한 뉴스 추천 시스템 만들기
## 시작 전 사전정보
* 마지막 수정날짜 : 2022-05-20
* 튜토리얼 난이도 : ★☆☆☆☆
* 읽는데 걸리는 시간 : 7분
* 사용 언어 : SQL(100%)
* 예제 파일 위치 : model/recommendation/data/news
* 참고문서 : [Latent Dirichlet Allocation 공식 논문](https://www.jmlr.org/papers/volume3/blei03a/blei03a.pdf), [Gensim Github](https://github.com/RaRe-Technologies/gensim)
* [이전 문서]() | [다음 문서]()

<br>

## 튜토리얼 소개 <br>

>**추천 시스템 이해하기 :** 추천시스템은 사용자가 전체 아이템이 아닌 소수의 아이템을 소비하고 평점을 주게 됩니다. 아직 소비하지 않은 아이템에 대해서 평점을 어떻게 줄지를 예측하고 높은 평점을 줄 것으로 예측되는, 하지만 소비되지 않은 아이템의 평점을 높은 순위부터 보여줍니다. <br>
>> 본 튜토리얼에서는 사용자가 소비한 아이템(뉴스) 내용(제목, 카테고리, 키워드 등)데이터를 사용하여 간단한 추천시스템을 구축합니다. 만약, 아이템 내용이 아닌 사용자가 부여한 평점데이터를 사용하여 추천시스템을 만들고 싶다면 다음 [영화 추천 시스템 만들기](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md) 튜토리얼을 참조하세요. 

<br>

네이버, 다음 등의 포탈에서 제공하는 온라인 뉴스는 하루에 수만건의 뉴스 기사들이 여러 언론사들로부터 제공되고 있습니다. 수만건의 최신뉴스 기사들이 나오는 상황은 사용자가 관심을 가지고 싶어하는 뉴스 기사를 찾기 어렵게 만듭니다. 이와 같은 문제를 해결하기 위해 기획자들은 프로덕트에 추천시스템을 적용하여 고객의 만족을 높이기위해 기획합니다.  <br>
ThanoSQL은 추천시스템 알고리즘을 자동화하여 머신러닝 지식이 없어도 플랫폼 실무자들이 간단히 쿼리구문을 통해 추천 시스템을 구축할 수 있도록 도와줍니다. <br><br>

아래는 ThanoSQL 뉴스 추천시스템의 활용예시 입니다.

* 온라인서점 플랫폼에서 도서 별 요약 내용을 바탕으로 고객이 좋아할만한 도서를 추천 해줄 수 있습니다. 단순하게 주문량과 평점을 통한 추천이 아닌 실제 내용을 바탕으로 유저의 취향에 맞는 도서를 추천하여 고객의 만족도를 높일 수 있습니다. 

* 실시간으로 수만개의 글이 올라오는 온라인 커뮤니티사이트에서 추천시스템을 적용하여 유저 별 좋아하는 글들을 보여줄 수 있습니다. 고객은 수만개의 글들 중에서 자신이 관심 있어하는 글들을 놓치지 않고 볼 수 있습니다.   

<br>

이번 튜토리얼에서는 `Microsoft News Dataset` 을 사용합니다. `Microsoft News Dataset` 데이터 세트는 대표적인 뉴스 데이터 세트로 MSN에 있는 뉴스 기사들을 모아놓은 데이터 세트입니다. <br>
ThanoSQL을 사용하여 간단하게 사용자가 관심있어 할 뉴스를 추천해주는 시스템을 만들어봅니다.

<br>

## 1. 데이터세트 확인

뉴스 추천 모델을 만들기 위해 우리는 두개의 데이터 세트를 사용합니다. 데이터 세트는 ThanoSQL DB 에 저장되어 있습니다. 아래의 쿼리 구문을 실행하여 `mind_train_user_history`, `news_train`  테이블들을 확인해봅시다. 

#### **mind_train_user_history**

아래의 쿼리구문을 실행하여 유저가 소비한 뉴스 히스토리를 확인합니다.

```python
%%thanosql 
SELECT * FROM mind_train_user_history LIMIT 5
```



<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>news_id</th>
      <th>ord</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>U100</td>
      <td>N20121</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>U100</td>
      <td>N33998</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>U100</td>
      <td>N45954</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>U100</td>
      <td>N55743</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>U100</td>
      <td>N50095</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>

> **데이터 이해하기**<br>
`mind_train_user_history` 데이터 세트는 `user_id`: 유저 아이디, `news_id`: 유저가 본 뉴스 아이디, `ord`: 뉴스를 본 순서의 정보가 포함되어 있습니다. <br> 

#### **news_train**

아래의 쿼리구문을 실행하여 ThanoSQL DB에 저장되어 있는 `news_train` 샘플 데이터세트를 확인합니다.

```python
%%thanosql 
SELECT * FROM news_train LIMIT 5
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>category</th>
      <th>subcategory</th>
      <th>title</th>
      <th>abstract</th>
      <th>url</th>
      <th>title_entities</th>
      <th>abstract_entities</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>N53790</td>
      <td>health</td>
      <td>wellness</td>
      <td>I turned 40 and got my first mammogram. Now, I...</td>
      <td>There are a few milestone birthdays that are c...</td>
      <td>https://assets.msn.com/labs/mind/AAJ0eGH.html</td>
      <td>[]</td>
      <td>[{"Label": "Breast Cancer Awareness Month", "T...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>N32665</td>
      <td>sports</td>
      <td>baseball_mlb</td>
      <td>Milwaukee Brewers skipper Craig Counsell named...</td>
      <td>Counsell finished second in 2018, can he take ...</td>
      <td>https://assets.msn.com/labs/mind/AAJQIbf.html</td>
      <td>[{"Label": "Craig Counsell", "Type": "P", "Wik...</td>
      <td>[{"Label": "Craig Counsell", "Type": "P", "Wik...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>N61168</td>
      <td>news</td>
      <td>newsscienceandtechnology</td>
      <td>Amazon Fire TV Stick 4K</td>
      <td>The 3rd-gen Fire TV Stick has been upgraded to...</td>
      <td>https://assets.msn.com/labs/mind/AADHmgh.html</td>
      <td>[{"Label": "Amazon Fire TV", "Type": "U", "Wik...</td>
      <td>[{"Label": "Amazon Fire TV", "Type": "U", "Wik...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>N55438</td>
      <td>finance</td>
      <td>finance-saving-investing</td>
      <td>13 investing rules you should break</td>
      <td>The best ways to invest money have been evolving.</td>
      <td>https://assets.msn.com/labs/mind/AAINQjS.html</td>
      <td>[]</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>N1315</td>
      <td>lifestyle</td>
      <td>lifestyleroyals</td>
      <td>Prince Harry, sans Meghan, makes first officia...</td>
      <td>Prince Harry arrived in Japan for the 2019 Rug...</td>
      <td>https://assets.msn.com/labs/mind/AAJJmH3.html</td>
      <td>[{"Label": "Prince Harry, Duke of Sussex", "Ty...</td>
      <td>[{"Label": "Prince Harry, Duke of Sussex", "Ty...</td>
    </tr>
  </tbody>
</table>
</div>

> **데이터 이해하기**<br>
`news_train` 데이터 세트는 뉴스기사의 아이디, 범주, 하위범주, 제목, 요약내용 등의 뉴스 기사에 대한 정보를 담고 있습니다. <br> 이번 튜토리얼에서는 뉴스기사의 `아이디`, `제목`, `요약 내용`의 속성 칼럼 만을 사용하여 모델을 만들어 봅니다. 


## 2. 개인화 뉴스추천 모델 빌드
이전 단계에서 확인한 `news_train` 샘플 데이터를 사용하여 개인화 뉴스추천 모델을 만듭니다.
아래 쿼리 구문을 실행시켜 news_rec 이라는 모델을 만들어 봅니다. 


```python
%%thanosql
BUILD MODEL news_rec 
USING LDAREC
OPTIONS(
        article_id ='id', 
        article_title='title',
        article_abstract='abstract', 
        user_history_tb_name='mind_train_user_history'
       )
AS SELECT * FROM news_train
```

> ### **쿼리 세부정보**
>```BUILD MODEL``` 쿼리구문을 사용하여 news_rec 이라는 모델을 만들고 학습시킵니다.
```OPTIONS(article_id='id', article_title='title', article_abstract='abstract', user_history_tb_name='mind_train_user_history', ...)``` 쿼리구문은 모델 생성에 필수적으로 필요한 news_train 샘플데이터 세트의 유저 아이디 칼럼이름, 문서제목칼럼, 문서내용칼럼, 유저히스토리 테이블 이름들을 할당하여 모델 빌드를 위한 데이터 세트 전처리 및 모델 학습이 가능하도록 합니다.  

## **3. 빌드 완료된 모델 사용하여 사용자에게 맞춤 뉴스추천 해보기**
이전 단계에서 빌드한 개인화 추천 모델을 사용해 유저가 좋아할 뉴스기사를 예측해 추천목록을 출력합니다.


```python
%%thanosql
PREDICT USING news_rec
OPTIONS(
        user_id = 'U9999', 
        nrec= 5
       ) 
AS SELECT * FROM news_train
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sim</th>
      <th>id</th>
      <th>category</th>
      <th>subcategory</th>
      <th>title</th>
      <th>abstract</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.996791</td>
      <td>N60258</td>
      <td>finance</td>
      <td>finance-real-estate</td>
      <td>$600K Inman Park loft is marketed as a modern ...</td>
      <td>Contemporary complex has stood since 2002 alon...</td>
      <td>https://assets.msn.com/labs/mind/BBWq1Yw.html</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.994752</td>
      <td>N45524</td>
      <td>news</td>
      <td>newspolitics</td>
      <td>FiveThirtyEight: Ivanka Trump will finally speak</td>
      <td>Walt Hickey and Farai Chideya look forward to ...</td>
      <td>https://assets.msn.com/labs/mind/AAIIljB.html</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.993490</td>
      <td>N46318</td>
      <td>finance</td>
      <td>finance-real-estate</td>
      <td>University Of Minnesota Architecture Dept. Des...</td>
      <td>This house near the University of Minnesota ca...</td>
      <td>https://assets.msn.com/labs/mind/BBWBWVp.html</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.992947</td>
      <td>N7695</td>
      <td>news</td>
      <td>newsus</td>
      <td>Founders Brewing settles racial discrimination...</td>
      <td>Founders Brewing has settled a racial discrimi...</td>
      <td>https://assets.msn.com/labs/mind/AAJF2qK.html</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.991475</td>
      <td>N52922</td>
      <td>foodanddrink</td>
      <td>newstrends</td>
      <td>NYC Rising Star Sommelier Accused of Sexual Mi...</td>
      <td>Anthony Cailan of Nolita restaurant the Usual ...</td>
      <td>https://assets.msn.com/labs/mind/AAJIlQ0.html</td>
    </tr>
  </tbody>
</table>
</div>

뉴스추천 결과 유저 `U9999`는 주로 정치 경제 관련 뉴스 기사에 대해 관심이 많은 유저인 것을 확인 할 수 있습니다.  

>### **쿼리 세부정보**
>```PREDICT USING``` 쿼리는 이전 단계에서 생성한 news_rec 이라는 모델을 사용하여 예측하게 합니다.
```OPTIONS(user_id='U9999', nrec=5 )``` 쿼리는 특정 유저 아이디 `U9999`의 추천 뉴스 `5개`를 예측하여 출력하게 합니다.

<br>

## 튜토리얼을 마치며

이번 튜토리얼에서는 Microsoft news dataset 을 사용하여 뉴스 추천모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 추천 시스템은 각 프로덕트에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있고 평점기반 추천모델 등과 함께 사용하여 성능을 더욱 높일 수 있습니다. 뉴스 추천 시스템은 뉴스뿐만이 아닌 문서로 된 아이템만 있다면 다양한 분야에서 적용이 가능합니다. <br> 
온라인서점에 적용하여 유저에게 도서 추천, 커뮤니티 사이트의 글 추천 등 뉴스추천시스템을 통해 나만의 경쟁력있는 플랫폼을 완성 할 수 있습니다. 내 서비스를 위한 나만의 추천시스템 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요. <br>
* [나만의 데이터 업로드하기](comingsoon)
* [중급 추천시스템 만들기](comingsoon) 
* [나만의 추천시스템 배포하기](comingsoon)
 
<br>

## 나만의 서비스를 위한 모델배포 관련 문의 <br>
ThanoSQL로 만든 모델을 나만의 플랫폼에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

추천시스템 구축 관련 문의: contact@smartmind.team
