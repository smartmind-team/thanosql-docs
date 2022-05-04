# **ThanoSQL LDA Article Recommendation system**
## **Tutorial Guideline :**
이번 튜토리얼에서는 데이터 분석가에게 LDA (Latent Dirichlet Allocation) 토픽 모델링을 활용한 문서 추천 시스템을 소개합니다. ThanoSQL을 사용하면 표준 SQL 쿼리문을 통해 머신러닝 모델을 만들고 실행할 수 있습니다. 목표는 SQL 실무자가 광범위한 프로그래밍 스킬, 머신러닝에 대한 지식 없이도 간단한 쿼리문으로 모델을 빌드할 수 있도록 지원하여 머신러닝을 대중화하고 데이터 이동의 필요성을 제거하여 개발 속도를 향상시키는 것입니다.

이 가이드에서는 Microsoft News 데이터 세트를 사용하여 LDA 추천시스템을 알아봅니다. 

## **Model Description :**
> 모델 설명 : LDA 토픽모델링은 각 단어나 문서들의 집합에 대해 숨겨진 주제를 찾아내어 문서나 키워드별로 주제끼리 묶어주는 비지도학습 알고리즘 중 하나입니다. Gensim에서 제공하는 오픈소스를 활용하여 LDA모델을 만들고 유저별 토픽 스코어를 학습합니다. 학습을 통해 얻은 스코어를 기반으로 유저가 관심있어하는 토픽을 추천합니다. 

## **Intended uses & Limitation**
- 먼저 ThanoSQL LDA 추천 모델은 SQL 실무자가 별도의 코딩작업 없이 간단하게 쿼리문만으로 추천 모델을 만들수 있도록 도와줍니다.
- LDA 추천 모델을 사용하기 위해서는 유저 별 클랙했던 기사들의 정보가 담겨 있는 `user_history` 테이블과 뉴스기사별 정보가 담겨 있는 `news_train` 테이블이 필요합니다.
- Python, Java, 또는 C 언어등과 같은 광범위한 프로그래밍 스킬, 머신러닝에 대한 깊은 이해 없이 표준 SQL 쿼리문으로 간단하게 모델 빌드를 위한 데이터 전처리를 작업을 할 수 있습니다.
- SQL 실무자는 빌드된 모델을 사용하여 Predict 함수를 통해 특정 유저가 관심있어 할 뉴스기사 dataframe을 출력할 수 있습니다.
- 이 튜토리얼에서는 51282 개의 문서가 담겨있는 `news_train` 테이블과 926058 rows 의 유저 히스토리가 담겨 있는 `mind_train_user_history` 테이블을 사용합니  다.  
- ThanoSQL LDAREC 모델은 전처리 과정에 Tokenize 과정이 포함되어 있어 데이터 양이 많아지면 모델 학습 속도가 느려질 수 있습니다

<br>
<br>

# **MIND_NEWS 데이터셋을 사용하여 개인화 뉴스 추천모델 만들어보기**
##  **1.1 데이터셋 ThanoSQL DB 에 업로드**
로컬환경에 저장되어 있는 파일을 표준 SQL 쿼리를 사용하여 ThanoSQL DB에 업로드 합니다.
이번 튜토리얼에 사용되는 `news_train`, `mind_user_history` 테이블들은 이미 업로드가 되어 있습니다. 아래의 쿼리문을 사용하여 ThanoSQL DB에 테이블을 저장 할 수 있습니다.   

```postgresql
CREATE TABLE news_train
(
    id                  int not null,
    category            int,
    subcategory         float,
    title               varchar not null
    abstract            varchar not null    
    url                 varchar not null  
    title_entities      varchar 
    abstract_entities   varchar
);
COPY news_train from '/your local data directory/news_train.csv' delimiter ',' csv header;
```

## **1.2 데이터셋 확인**
ThanoSQL DB에 저장되어 있는 `news_train` 샘플 데이터 셋을 표준 SQL 쿼리를 사용하여 확인합니다.

`news_train` 데이터셋은 'id', 'category', 'subcategory', 'title', 'abstract' 정보를 담고 있는 테이블입니다. <br> 이번 튜토리얼에서는 LDA 모델 빌드를 위해 'id', 'title', 'abstract' 3개의 칼럼만 사용합니다. 이 데이터셋은 ThanoSQL DB에 저장되어 있어 아래의 쿼리를 실행하여 불러올 수 있습니다.  


```python{toggle}
%thanosql SELECT * FROM news_train LIMIT 5
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <td>N61118</td>
      <td>health</td>
      <td>medical</td>
      <td>Englewood Health merging with Hackensack Meridian</td>
      <td>The deal includes plans for $400 million in ne...</td>
      <td>https://assets.msn.com/labs/mind/AAIOYBC.html</td>
      <td>[{"Label": "Englewood Hospital and Medical Cen...</td>
      <td>[{"Label": "Englewood, New Jersey", "Type": "G...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>N46766</td>
      <td>autos</td>
      <td>autosnews</td>
      <td>Troopers said one driver crossed over into lan...</td>
      <td>Two women were killed in a head-on collision o...</td>
      <td>https://assets.msn.com/labs/mind/AAJrnsq.html</td>
      <td>[]</td>
      <td>[{"Label": "DeLeon Springs, Florida", "Type": ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>N1151</td>
      <td>sports</td>
      <td>golf</td>
      <td>Hovland (69) extends PGA Tour record of consec...</td>
      <td>A day after setting a PGA Tour record with his...</td>
      <td>https://assets.msn.com/labs/mind/AAIYwOs.html</td>
      <td>[{"Label": "Viktor Hovland", "Type": "N", "Wik...</td>
      <td>[{"Label": "Viktor Hovland", "Type": "N", "Wik...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>N29681</td>
      <td>health</td>
      <td>health-news</td>
      <td>Retailers are pulling Johnson's baby powder fr...</td>
      <td>Retailers including CVS, Walgreens, Rite Aid a...</td>
      <td>https://assets.msn.com/labs/mind/AAJm5wa.html</td>
      <td>[]</td>
      <td>[{"Label": "CVS Pharmacy", "Type": "O", "Wikid...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>N29938</td>
      <td>news</td>
      <td>newspolitics</td>
      <td>The D'Alesandro family: a Baltimore political ...</td>
      <td>The D'Alesandro family, a political dynasty wi...</td>
      <td>https://assets.msn.com/labs/mind/AAJ67P4.html</td>
      <td>[{"Label": "Thomas D'Alesandro III", "Type": "...</td>
      <td>[{"Label": "Thomas D'Alesandro III", "Type": "...</td>
    </tr>
  </tbody>
</table>
</div>



`mind_train_user_history` 데이터셋은 'user_id': 유저 아이디, 'news_id': 유저가 본 뉴스 아이디, 'ord': 뉴스를 본 순서의 정보가 포함되어 있습니다. <br> 
이 데이터 셋은 ThanoSQL DB 에 저장되어 있어 아래의 쿼리를 실행하여 불러올 수 있습니다. 아래의 예시는 상위 5개 로우만 불러오는 쿼리문입니다.


```python
%thanosql SELECT * FROM mind_train_user_history LIMIT 5
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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



## **2. LDA 추천 모델 빌드**
이전 단계에서 확인한 `news_train` 샘플 데이터를 사용하여 LDA 추천 모델을 만듭니다. 샘플데이터로부터 '문서 ID', '문서 제목', '문서 내용'들의 정보가 담겨있는 칼럼들 이름과 '유저 히스토리'정보를 포함하고 있는 테이블 이름'을 지정해주며 LDA 추천 모델을 만들고 학습합니다.


```python
%%thanosql
BUILD MODEL news_rec 
USING LDAREC
OPTIONS(
    article_id ='id', 
    article_title='title',
    article_abstract='abstract', 
    user_history_tb_name='mind_train_user_history')
AS SELECT * FROM news_train
```



### **쿼리 세부정보**
>```BUILD MODEL``` 쿼리를 사용하여 news_rec 이라는 모델을 만들고 학습시킵니다.
>```OPTIONS(article_id='id', article_title='title', article_abstract='abstract', user_history_tb_name='mind_train_user_history', ...)``` 쿼리는 모델 생성에 필수적으로 필요한 news_train 샘플데이터셋의 유저 아이디 칼럼이름, 문서제목칼럼, 문서내용칼럼, 유저히스토리 테이블 이름들을 할당하여 데이터셋 전처리 및 모델 빌드가 가능하도록 합니다.  

## **3. 빌드 완료된 모델 사용하여 사용자에게 맞춤 뉴스추천 해보기**
이번 단계에서는 이전 단계에서 빌드한 LDA 추천 모델을 사용하여 유저가 좋아할만한 뉴스 혹은 문서를 예측하여 추천합니다.


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
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <td>0.997407</td>
      <td>N63230</td>
      <td>sports</td>
      <td>football_nfl</td>
      <td>Come tailgate with BGN and Audible's '64th Man...</td>
      <td>Audible Original 64th Man</td>
      <td>https://assets.msn.com/labs/mind/BBWEJCi.html</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.994604</td>
      <td>N26672</td>
      <td>tv</td>
      <td>tvnews</td>
      <td>LeBron James Is The Latest To Announce A New T...</td>
      <td>This sounds like a must-see docuseries.</td>
      <td>https://assets.msn.com/labs/mind/AAJv2z1.html</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.994534</td>
      <td>N32182</td>
      <td>music</td>
      <td>cma-awards</td>
      <td>Carrie Underwood Praises Miranda Lambert as 'S...</td>
      <td>Carrie Underwood Praises Miranda Lambert as 'S...</td>
      <td>https://assets.msn.com/labs/mind/BBWE9aK.html</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.994524</td>
      <td>N27910</td>
      <td>lifestyle</td>
      <td>lifestylerelationships</td>
      <td>Euclid waitress gets $1,070 tip at Mama Catena...</td>
      <td>None</td>
      <td>https://assets.msn.com/labs/mind/AAJuMwm.html</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.994521</td>
      <td>N64556</td>
      <td>tv</td>
      <td>tvnews</td>
      <td>Clark County Common Pleas Court cases</td>
      <td>Auditor's Office;</td>
      <td>https://assets.msn.com/labs/mind/BBWygV7.html</td>
    </tr>
  </tbody>
</table>
</div>



>### **쿼리 세부정보**
>```PREDICT USING``` 쿼리는 이전 단계에서 생성한 news_rec 이라는 모델을 사용하여 예측하게 합니다.
```OPTIONS(user_id='U9999', nrec=10 )``` 쿼리는 특정 유저 아이디 'U9999'의 추천 뉴스 '5개'를 예측하여 출력하게 합니다.


```python

```
