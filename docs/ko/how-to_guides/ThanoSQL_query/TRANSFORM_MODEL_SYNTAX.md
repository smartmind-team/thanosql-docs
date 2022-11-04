---
title: TRANSFORM
---

# __TRANSFORM__

## __1. TRANSFORM 문__

사용자는 "__TRANSFORM__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델 생성시 사용한 동일한 전처리 방법을 적용할 수 있습니다. 

## __2. TRANSFORM 구문__ 
```sql
%%thanosql
TRANSFORM USING [기존 학습한 모델 이름]
AS
[사용할 테스트 데이터 세트]
```

## __3. TRANSFORM 예시__ 

!!! note 
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트가 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Pre-built Model Statement Reference](/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있는 점 참고해 주세요.

```sql
%%thanosql
TRANSFORM USING mymodel 
AS 
SELECT * 
FROM titanic_test 
```
