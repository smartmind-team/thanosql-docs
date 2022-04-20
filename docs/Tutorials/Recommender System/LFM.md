# ThanoSQL LightFM Recommender System

## Model Description
LightFM is a Python implementation of a number of popular recommendation algorithms for both implicit and explicit feedback. We adopted Lyst's LightFM recommender model and customized it for data analysts or anyone who want to provide customers personalized product recommendations.
It makes it possible to incorporate both item and user metadata into the traditional matrix factorization algorithms. It represents each user and item as the sum of the latent representations of their features, thus allowing recommendations to generalise to new items (via item features) and to new users (via user features).

<br>

## Intended uses & Limitation
This model can be used in various fileds where have explicit feedback data. We suggest a data analyst in ecommerce industry to employ this model for recommending of the most atractive merchandises to their shoppers.

    

(__“Wow! I want to use it!”__ moment _right_ here…) <br>
(“Wow! I *want* to use it!” moment **right here**…) <br>
(“Wow! I want to use it!” moment ***right here***…)

<br>



## How to use
In order to use this model, you must follow following steps:
> 1. Prepare your data
>> we have to have image and label columns
>2. Build your model
>>    what is this





## Tutorial Guideline : 
이번 튜토리얼에서는 데이터 분석가에게 LightFM 추천 모델을 소개합니다. ThanoSQL을 사용하면 표준 SQL 쿼리문을 통해 머신러닝 모델을 만들고 실행할 수 있습니다. 목표는 SQL 실무자가 광범위한 프로그래밍 스킬, 머신러닝에 대한 지식 없이도 간단한 쿼리문으로 모델을 빌드할 수 있도록 지원하여 머신러닝을 대중화하고 데이터 이동의 필요성을 제거하여 개발 속도를 향상시키는 것입니다.

이 가이드에서는 Kaggle의 Movielens 데이터 세트와 Goodbooks 데이터 세트를 사용하여 평점(explicit-feedback)을 활용한 LFM모델을 만들어 아이템 ID와 사용자 ID를 기반으로 추천 리스트를 생성하는 방법을 알아봅니다. 

Movielens 데이터 세트에는 사용자가 영화에 부여한 평점(1~5점)과 장르 등의 영화 메타데이터가 포함됩니다. 
Goodbooks 데이터 세트에는 사용자가 도서에 부여한 평점(1~10점)과 장르 등의 도서 메타데이터가 포함됩니다.


## Model Description :
	- 모델 설명 : Light FM 모델은 추천시스템의 Cold-start 문제를 최소화 시키기 위해 Lyst사에서 제안된 모델로 Content-based 와 Collaborative Filtering의 장점을 결합한 하이브리드 모델입니다. 메타데이터의 아이템과 유저를 행렬분해알고리즘 (Matrix Factorization)을 사용하여 Latent vector로 표현되고 벡터의 합으로 계산되어 새로운 유저나 아이템을 추천할 수 있습니다. 


## Intended uses & Limitation
- 먼저 ThanoSQL LFM 모델은 SQL 실무자가 별도의 코딩작업 없이 간단하게 쿼리문만으로 추천리스트를 만들수 있도록 도와줍니다. 
- 'UserID', 'ItemID', 'Rating' 칼럼이 존재하는 테이블만 있으면 추천모델을 만들 수 있습니다.
- Python, Java, 또는 C 언어등과 같은 광범위한 프로그래밍 스킬, 머신러닝에 대한 깊은 이해 없이 표준 SQL 쿼리문으로 간단하게 모델 빌드를 위한 데이터 전처리를 작업을 할 수 있습니다.
- SQL 실무자는 빌드된 모델을 사용하여 Predict 함수를 통해 User to Item, Item to User, Item to Item 세가지 형식으로 추천 리스트를 만들수 있습니다.
- LFM 모델은 Matrix Factorization 알고리즘을 사용하기 때문에 데이터가 커지면 계산량이 많아져 컴퓨터에 과부화가 올수 있습니다. (데이터의 크기가 일정기준 이상 넘어가면 시간기준으로 테이블을 분리하여 추천 모델을 만드는것을 추천합니다)


## Movie-lens데이터셋을 사용하여 평점기반 추천 모델 구축과 추천 리스트 생성해보기 
### 데이터셋 확인
ThanoSQL DB에 저장되어 있는 Movielens 샘플 데이터 셋을 표준 SQL 쿼리를 사용하여 확인합니다.

Movielens_train 데이터셋은 'userid', 'movieid', 'rating', 'title' 정보를 담고 있는 테이블입니다. 이 데이터셋은 ThanoSQL DB에 저장되어 있어 아래의 쿼리를 실행하여 불러올 수 있습니다.  
```POSTGRESQL
SELECT * FROM movielens_train
```
![movie_train](./movie_train.png)

### **추천 모델 빌드**
이전 단계에서 확인한 Movielens 샘플 데이터를 사용하여 명시적 추천 모델을 만듭니다. 샘플데이터로부터 '사용자ID', '아이템ID', '평점' 칼럼 이름을 지정해주고 LFM 모델을 만들고 학습합니다.
```POSTGRESQL
BUILD MODEL movie_rec 
USING Light_FM 
OPTIONS (   user_col='userid', 
            item_col='movieid',
            rating_col='rating', 
            item_names= 'title'
        ) 
AS SELECT * FROM movielens_train
```
### **쿼리 세부정보**
```BUILD MODEL``` 명령어를 사용하여 movie_rec 이라는 모델을 만들고 학습시킵니다. 

```OPTIONS(user_col='userid',item_col='movieid',rating_col='rating', item_names= 'title', ...)```




(if exist) Using pre-trained model
- How to build my own model using pre-trained

```POSTGRESQL
SELECT * FROM books_train
```
![books_train](./books_train.png)

