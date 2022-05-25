# __모델 재학습하기 (FIT MODEL)__

## __1. FIT MODEL 구문 개요__ 

사용자는 "__FIT MODEL__" 구문만을 사용하여 이미 학습된 모델에 새롭게 데이터를 추가하여 학습시킬 수 있습니다. 

!!! tip 
    "__FIT MODEL__" 구문은 최초 학습 이후 데이터가 추가적으로 확보되어 기존 모델을 업데이트 하거나, 사전 학습된 인공지능 모델을 사용자만의 데이터로 학습 시킬 경우에 사용하는 것을 권장합니다.


## __2.FIT MODEL 구문__
```sql
%%thanosql
FIT MODEL [사용자 지정 모델 이름]
USING [기존 학습한 모델 이름 | 사전 학습된 인공지능 모델 이름 ]
OPTIONS ([, 인공지능 모델을 만들 때 필요한 옵션값])
AS
[사용할 데이터 세트]
``` 
!!! NOTE
    "__OPTIONS__"에서 사용하는 옵션값은 사용할 인공지능 모델에 따라 다르게 적용됩니다. 각 인공지능 모델에 대한 "__OPTIONS__"는 [다음 문서]()에서 확인 가능합니다.

!!! warning
    다만 Auto-ML 모델의 경우, 기존 인공지능 모델이 가지고 있던 파라미터 값을 사용하는 것이 아닌 데이터 세트만 바꿔 "__OPTIONS__"에 사용된 옵션값에 따른 모델을 만듭니다.

## __3.FIT MODEL 구문 예시__
아래 예는 "__FIT MODEL__" 구문을 사용하여 사용자가 이전에 만들었던 <mark style="background-color:#E9D7FD ">test_classifier</mark> 모델에 새롭게 추가된 데이터 세트를 사용하여 학습한  <mark style="background-color:#E9D7FD ">fit_test_classifier</mark> 모델을 만듭니다.

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin']
    )
AS
SELECT *
FROM titanic_train
```