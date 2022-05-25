# __모델 학습하기 (BUILD MODEL)__ 

## __1.BUILD MODEL 구문 개요__

사용자는 데이터 과학(Data Science)에 대한 전문 지식이 없어도 간단하게 "__BUILD MODEL__" 구문만을 사용하여 원하는 인공지능 모델을 개발할 수 있습니다.

## __2.BUILD MODEL 구문__

```sql
%%thanosql
BUILD MODEL [사용자 지정 모델 이름]
USING [사용할 인공지능 모델]
OPTIONS ([,인공지능 모델을 만들 때 필요한 옵션값}])
AS 
[사용할 데이터 세트]

```

!!! NOTE
    "__OPTIONS__"에서 사용하는 옵션값은 사용할 인공지능 모델에 따라 다르게 적용됩니다. 각 인공지능 모델에 대한 "__OPTIONS__"는 [다음 문서]()에서 확인 가능합니다.

## __3.BUILD MODEL 구문 예시__

### __3-1.추천 모델 생성을 위한 "Light_FM" 인공지능 모델 사용__ 

아래 예는 "__BUILD MODEL__" 구문을 사용하여 사용자가 정의한 <mark style="background-color:#E9D7FD ">user_rec</mark> 모델을 ["__Light_FM__"](https://arxiv.org/pdf/1507.08439.pdf)이라는 인공지능 모델을 사용하여 추천 모델을 만듭니다. 전체 과정이 궁금하다면, [Movielens 영화 평점 데이터 세트를 사용하여 영화 추천 모델 만들기]()를 진행해 보세요.

```sql
%%thanosql
BUILD MODEL user_rec
USING Light_FM
OPTIONS (
    user='userid',
    item='movieid',
    target='rating',
    description='title' 
    )
AS 
SELECT * 
FROM movielens_train 
```
!!! tip
    "__OPTIONS__"에 따로 옵션값을 할당하지 않으면 기본값으로 알고리즘 모델이 학습됩니다. 알고리즘 모델에서 사용 가능한 옵션들에 대한 추가적인 설명은 [참조 페이지](https://making.lyst.com/lightfm/docs/lightfm.html)를 통해 확인할 수 있습니다. 

### __3-2.분류 모델 생성을 위한 Auto_ML 모델 사용__

아래 예는 "__BUILD MODEL__" 구문을 사용하여 사용자가 정의한 <mark style="background-color:#E9D7FD ">test_classifier</mark> 모델을 ThanoSQL에서 제공하는 ["AutomlClassifier"](https://www.google.com/search?q=automl&oq=automl&aqs=chrome.0.69i59j35i39j0i512j69i60l5.3258j0j4&sourceid=chrome&ie=UTF-8) 모델을 사용하여 분류 모델을 만듭니다. 전체 과정이 궁금하다면, [Auto-ML을 사용하여 타이타닉 생존자 분류 모델 만들기]를 진행해 보세요.

```sql
%%thanosql
BUILD MODEL titanic_classification 
USING AutomlClassifier 
OPTIONS (
    target='survived', 
    impute_type='iterative',  
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin']
    ) 
AS 
SELECT * 
FROM titanic_train
```

!!! note "'__BUILD MODEL__'을 통해 사용 가능한 인공지능 모델"
    - Auto-ML 분류 모델 - AutomlClassifier
    - Auto-ML 회귀 모델 - AutomlRegressor
    - 추천 모델 - Light_FM
    - 추천 모델 - LDAREC


