# 이미지 분류

이미지 안에 있는 것을 식별하는 작업을 이미지 분류라 합니다. 일반적으로 이미지 분류는 단일 개체가 포함된 이미지를 구분하는 데 사용됩니다.

이 예제에서는 캐글의 Dogs vs. Cats 데이터세트를 이용하여 이미지에 고양이가 있는지, 개가 있는 지를 ThanoSQL을 통해 예측하는 작업을 해보겠습니다.

### 모델 설명

ConvNeXt는 2022년 1월 Facebook AI Research에서 발표한 논문 'A ConvNet for the 2020s'에 소개된 이미지 모델로, 기존의 CNN구조에 transformer 구조와 최신 기법들을 적용한 모델입니다.

<br>

### Intended uses & Limitation

- 현재 이 모델은 하나의 이미지에서 하나의 레이블을 예측하는 용도로 사용할 수 있습니다.

- 이미지의 경로를 담은 열과, 이미지의 레이블을 담은 열이 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 이미지 분류 모델 빌드하기

### 1. ThanoSQL DB에 데이터셋 업로드

데이터세트는 `training_set` 폴더와 `test_set` 폴더로 나뉘어 있고, 각각의 폴더는 하위 폴더에 `cats` 폴더와 `dogs` 폴더로 나뉘어 고양이와 개 사진을 담고 있습니다.

```
Cat and Dog
├─training_set
│   └─training_set
│       ├─cats
│       │   ├─cat.1.jpg
│       │   ├─cat.2.jpg
│       │   ...
│       └─dogs
│           ├─dog.1.jpg
│           ├─dog.2.jpg
│           ...
└─test_set
    └─test_set
        ├─cats
        │   ├─cat.4001.jpg
        │   ├─cat.4002.jpg
        │   ...
        └─dogs
            ├─dog.4001.jpg
            ├─dog.4002.jpg
            ...
```

#### 1-1. image와 label 정보를 담은 csv파일 만들기

pandas를 사용하여 위와 같은 형태의 구조에서 이미지의 경로를 담은 열과 레이블 정보를 담은 열을 만들 수 있습니다.

```python
import pandas as pd
from pathlib import Path

def make_df(data_dir: str) -> pd.DataFrame:
    paths = list(Path(data_dir).rglob("*.jpg"))
    image = [str(p.resolve()) for p in paths]
    label = [p.stem.split(".")[0] for p in paths]
    df = pd.DataFrame({"image": image, "label": label})
    return df

train_data_root = "your local data directory/Cat and Dog/training_set"
test_data_root = "your local data directory/Cat and Dog/test_set"

train_df = make_df(train_data_root)
test_df = make_df(test_data_root)
```

#### 1-2. ThanoSQL DB에 csv파일 업로드하기

### 2. 데이터 확인

ThanoSQL DB에 저장되어 있는 image_tutorial 샘플 데이터셋을 SQL 쿼리를 통해 확인합니다.

이 데이터셋은 캐글의 Cat and Dog 데이터로, 약 10000장의 고양이와 개 사진을 담고 있습니다. 이 중 8000장은 훈련 데이터이고, 나머지 2000장은 테스트 데이터입니다.

다음 쿼리문을 통해 훈련 데이터를 확인해볼 수 있습니다.

```sql
%%thanosql
SELECT image, label FROM cat_and_dog_train
```

```
                                                  image label
0     .../Cat and Dog/training_set/training_set/dogs/dog...   dog
1     .../Cat and Dog/training_set/training_set/dogs/dog...   dog
2     .../Cat and Dog/training_set/training_set/dogs/dog...   dog
3     .../Cat and Dog/training_set/training_set/dogs/dog...   dog
4     .../Cat and Dog/training_set/training_set/dogs/dog...   dog
...                                                 ...   ...
8000  .../Cat and Dog/training_set/training_set/cats/cat...   cat
8001  .../Cat and Dog/training_set/training_set/cats/cat...   cat
8002  .../Cat and Dog/training_set/training_set/cats/cat...   cat
8003  .../Cat and Dog/training_set/training_set/cats/cat...   cat
8004  .../Cat and Dog/training_set/training_set/cats/cat...   cat
```

### 3. 이미지 분류 모델 빌드

위의 Cat and Dog 데이터를 사용하여 이미지 분류 모델을 만들어 보겠습니다. 사용할 모델은 ViT_Tiny입니다.

샘플 데이터에서 image 열과 label 열의 이름을 지정해주고 모델을 만들고 학습합니다.

```sql
%%thanosql

BUILD MODEL my_image_classifier
USING ConvNeXt_Tiny
OPTIONS (image_col='image', label_col='label')
AS SELECT * FROM cat_and_dog_train
```

### 4. 빌드된 모델을 사용하여 이미지 예측

#### 4-1. 훈련 데이터에 대한 예측

`PREDICT`를 사용하면 빌드된 모델을 사용하여 예측을 진행할 수 있습니다.

```sql
%%thanosql
PREDICT USING my_image_classifier
AS SELECT * FROM cat_and_dog_train
```

만약 결과를 파이썬 변수로 받고싶다면 다음처럼 하면 됩니다.

```sql
train_result = %thanosql PREDICT USING my_image_classifier AS SELECT * FROM cat_and_dog_train
```

```python
print(train_result.head())
```

```
                                               image label predicted
0  /home/dowon/notebook/data/Cat and Dog/training...   cat       cat
1  /home/dowon/notebook/data/Cat and Dog/training...   cat       cat
2  /home/dowon/notebook/data/Cat and Dog/training...   cat       cat
3  /home/dowon/notebook/data/Cat and Dog/training...   cat       cat
4  /home/dowon/notebook/data/Cat and Dog/training...   cat       cat
```

#### 4-2. 테스트 데이터에 대한 예측과 정확도 확인

위와 같은 방법을 사용해 테스트 데이터에 대해 결과를 예측하고 그 결과를 데이터프레임으로 저장합니다.

```sql
test_result = %thanosql PREDICT USING my_image_classifier AS SELECT * FROM cat_and_dog_test
```

```python
print(test_result.head())
```

```
                                               image label predicted
0  /home/dowon/notebook/data/Cat and Dog/test_set...   cat       cat
1  /home/dowon/notebook/data/Cat and Dog/test_set...   cat       cat
2  /home/dowon/notebook/data/Cat and Dog/test_set...   cat       cat
3  /home/dowon/notebook/data/Cat and Dog/test_set...   cat       cat
4  /home/dowon/notebook/data/Cat and Dog/test_set...   cat       cat
```

예측 정확도를 확인해보겠습니다.

```python
from sklearn.metrics import accuracy_score

accuracy_score(test_result["label"], test_result["predicted"]))
```

```
0.9822046465645081
```

98% 이상의 데이터에 대해 정확히 예측했다는 것을 알 수 있습니다.
