# **{튜토리얼 제목 기재}**
## **시작 전 사전정보**
* 튜토리얼 난이도 : **<ins>★☆☆☆☆**</ins>
* 읽는데 걸리는 시간 : **<ins>{분 단위로 기재}**</ins> <br>
ex) 7분
* 사용 언어 : **<ins>{사용언어 % 단위로 기재}**</ins> <br>
ex) SQL(100%)
* 참고문서 : **<ins>{데이터 세트 공식문서 링크}, {모델 공식문서 링크}**</ins> <br>
ex) [Movielens 데이터셋 페이지](https://grouplens.org/datasets/movielens/), [LightFM 알고리즘 기술문서](https://making.lyst.com/lightfm/docs/home.html)
* 바로 따라해보기 : **<ins>{튜토리얼 환경 링크 제공 예정}**</ins>


<br> 

## **튜토리얼 소개 :** <br>

>**<ins>{모델} 이해하기 : {모델}은 ... 모델에 대한 설명 추가</ins>** <br>
ex) 추천시스템은 사용자가 전체 아이템이 아닌 소수의 아이템을 소비(이 경우에는 시청)하고 평점을 주게 됩니다. 아직 소비하지 않은 아이템에 대해서 평점을 어떻게 줄지를 예측하고 높은 평점을 줄 것으로 예측되는, 하지만 소비되지 않은 아이템의 평점을 높은 순위부터 보여줍니다. <br>

**<ins>{모델}의 실생활 적용 사례 및 중요성 설명</ins>** <br>
ex) 넷플릭스등의 OTT 서비스등을 사용하다보면 'OO님이 좋아할만한 영화', 'OO님이 좋아할만한 드라마' 등의 제목 아래 흥미로운 컨텐츠 리스트들을 볼 수 있습니다. OTT 서비스 외에도 온라인쇼핑몰, 온라인 뉴스, 온라인 광고 등 온라인 플랫폼 시장에는 유저를 사로잡기 위한 다양한 추천시스템들이 존재합니다. 이처럼 플랫폼의 고객유치와 타 플랫폼과의 경쟁력을 높이기 위해 추천시스템의 중요도는 계속 올라가고 있습니다.
ThanoSQL은 추천시스템 알고리즘을 자동화하여 머신러닝 지식이 없어도 플랫폼 실무자들이 간단히 쿼리구문을 통해 추천 시스템을 구축할 수 있도록 도와줍니다. 
<br><br> 

**<ins>{모델}의 활용 및 기대효과</ins>** <br>
ex)
아래는 ThanoSQL 추천시스템의 활용 및 기대효과 예시입니다.

* 온라인 플랫폼 내에서 유저에게 항상 정적인 아이템 리스트를 보여주는 것보다 추천 시스템을 활용해 유저의 과거구매 이력, 평점 등의 정보를 바탕으로 고객이 관심있어 할 아이템 리스트를 보여주어 매출 증대 효과를 기대할 수 있습니다.    

* 플랫폼 내에서 사용자와 사용자 간의 매칭 추천에 활용하여 사용자들의 만족도를 높일 수 있습니다. (숙박시설-고객, 상품(큐레이션)-고객, 동영상/콘텐츠-고객 매칭 등)
<br>

**<ins>튜토리얼 목표 설명</ins>** <br>
ex) 이번 튜토리얼에서는 <span style="background-color:#FDF5E3">`Movielens`</span> 영화평점 데이터셋을 사용합니다. 
<span style="background-color:#FDF5E3">`Movielens`</span> 데이터셋은 사용자가 특정 영화에 대해서 평점을 준 것을 모아놓은 데이터세트입니다. <br>
ThanoSQL을 사용하여 간단하게 사용자가 좋아할 영화목록을 추천하는 영화 추천 모델을 만들어 봅니다.

---
<br>

## **1. 데이터세트 확인**

아래 쿼리문을 실행하여 ThanoSQL DB에 저장되어 있는 <span style="background-color:#FDF5E3">`Movielens`</span> 영화평점 데이터셋을 확인합니다.

```python
%%thanosql 
SELECT * FROM movielens_train LIMIT 5
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>userid</th>
      <th>movieid</th>
      <th>rating</th>
      <th>title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>31</td>
      <td>1</td>
      <td>3.0</td>
      <td>Toy Story (1995)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>31</td>
      <td>110</td>
      <td>5.0</td>
      <td>Braveheart (1995)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>260</td>
      <td>5.0</td>
      <td>Star Wars: Episode IV - A New Hope (1977)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>31</td>
      <td>364</td>
      <td>3.0</td>
      <td>Lion King, The (1994)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>31</td>
      <td>527</td>
      <td>0.5</td>
      <td>Schindler's List (1993)</td>
    </tr>
  </tbody>
</table>
</div>



> **데이터 테이블 이해하기 :**<br> 
`유저 31번`이 `1번 영화(Toy Story(1995))`에 `평점 3점` 부여 <br>
`110번 영화 (Bravehear)`에 `평점 5점` 부여 <br>
`260번 영화 (star wars)`에 `평점 5점` 부여 ...



## **2. 추천 모델 빌드**

다음 쿼리 구문을 실행하여 추천 모델을 구축합니다.


```python
%%thanosql
BUILD MODEL movie_rec
USING Light_FM
OPTIONS (   user_col='userid',   
            item_col='movieid',
            rating_col='rating',
            item_names= 'title'
        )
AS SELECT * FROM movielens_train
```

>### **쿼리 세부정보**
>```BUILD MODEL``` 쿼리를 사용하여 movie_rec 이라는 모델을 만들고 학습시킵니다. <br>
>```OPTIONS(user_col='userid',item_col='movieid',rating_col='rating', item_names= 'title', ...)``` 쿼리는 모델 생성에 필수적으로 필요한 movielens 샘플데이터셋의 `유저아이디 칼럼명`, `아이템아이디 칼럼명`, `평점 칼럼명` 들을 할당하여 데이터셋 전처리 및 모델 빌드가 가능하도록 합니다.

## **3. 빌드 완료된 모델을 사용하여 새로운 데이터 예측**

다음 쿼리문을 실행하여 이전 단계에서 만든 추천 모델로 유저31번이 좋아할만한 아이템 10개 목록을 생성합니다.


```python
%%thanosql
PREDICT USING movie_rec
OPTIONS (predict_type='predict_user', user_id=31, nrec=10)
AS SELECT * FROM movielens_train
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Incredibles, The (2004)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Iron Man (2008)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Pirates of the Caribbean: The Curse of the Bla...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Night at the Museum (2006)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Finding Nemo (2003)</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Lord of the Rings: The Two Towers, The (2002)</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Avatar (2009)</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Spider-Man (2002)</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Monsters, Inc. (2001)</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Cars (2006)</td>
    </tr>
  </tbody>
</table>
</div>

>### **쿼리 세부정보**
>```PREDICT USING``` 쿼리는 이전 단계에서 생성한 movie_rec 이라는 모델을 사용하여 예측하게 합니다.
```OPTIONS(predict_type='predict_user', user_id=1, nrec=10, ...)``` 쿼리는 ```predict_type='predict_user', user_id=31, nrec=10``` 를 지정하여 유저ID 31번이 좋아할만한 아이템 10개를 예상하여 리스트를 만들어 출력합니다.

<br>

## **4. 튜토리얼을 마치며**
**<ins>튜토리얼 요약 설명</ins>** <br>
ex) 이번 튜토리얼에서는 <span style="background-color:#FDF5E3">`Movielens`</span> 데이터셋을 사용하여 영화 평점기반 추천모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 다음단계인  [중급 추천 시스템 만들기](comingsoon) 튜토리얼에서는 추천모델을 더욱 심도있게 다뤄봅니다. 

유사 튜토리얼 리스트 <br>
* [유사 튜토리얼1](comingsoon)
* [유사 튜토리얼2](comingsoon) 
* [유사 튜토리얼3](comingsoon)
  
<br>

## **나만의 추천시스템을 만드는 것에 어려움이 있나요?** <br>
추천시스템 구축 관련 문의: contact@smartmind.team


