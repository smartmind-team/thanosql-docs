# **ThanoSQL 무작정 따라하기** 
# **Movielens 영화평점 데이터를 이용한 영화 추천 시스템 만들기**
## **시작 전 사전정보**
* 튜토리얼 난이도 :
* 읽는데 걸리는 시간(실행시간) :
* 사전 지식(언어 / 머신러닝 수준 등) :
* 참고문서 : 

<br> 

## **튜토리얼 소개 :**
>**추천 시스템 이해하기 :** 추천시스템은 사용자가 전체 아이템이 아닌 소수의 아이템을 소비(이 경우에는 시청)하고 평점을 주게 됩니다. 아직 소비하지 않은 아이템에 대해서 평점을 어떻게 줄지를 예측하고 높은 평점을 줄 것으로 예측되는, 하지만 소비되지 않은 아이템의 평점을 높은 순위부터 보여줍니다.

넷플릭스등의 OTT 서비스등을 사용하다보면 'OO님이 좋아할만한 영화', 'OO님이 좋아할만한 드라마' 등의 제목 아래 흥미로운 컨텐츠 리스트들을 볼 수 있습니다. OTT 서비스 외에도 온라인쇼핑몰, 온라인 뉴스, 온라인 광고 등 온라인 플랫폼 시장에는 유저를 사로잡기 위한 다양한 추천시스템들이 존재합니다' <br>

이번 튜토리얼에서는 `Movielens` 영화평점 데이터셋을 사용하여 추천 모델을 만듭니다. `Movielens` 데이터셋은 사용자가 특정 영화에 대해서 평점을 준 것을 모아놓은 데이터세트입니다.

ThanoSQL을 사용하여 다른 유저들이 유사한 영화에 대해 준 평점과 타겟 유저가 준 평점을 비교하여, 타겟 유저가 소비하지 않은 영화에 대한 평점을 예측합니다.  


ThanoSQL을 사용하여 간단하게 사용자가 좋아할 영화목록을 추천하는 시스템을 만들어 봅니다.







# **영화 추천 모델 만들어보기**

## **1. 데이터세트 확인**

아래 쿼리문을 실행하여 ThanoSQL DB에 저장되어 있는 movielens 영화평점 데이터셋을 확인합니다.

```python
%thanosql SELECT * FROM movielens_train LIMIT 5
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

## **3. 빌드 완료된 모델을 사용하여 추천목록 생성하기**

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

## **4. 다음 단계**
튜토리얼을 모두 끝내셨다면 다음단계 ㅇㅇㅇ ,
<br>나만의 추천시스템을 만들어 보세요
<br>나만의 웹사이트에 
 
추후 추가 예정 
