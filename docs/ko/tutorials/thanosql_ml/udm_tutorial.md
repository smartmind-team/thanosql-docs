---
title: User Defined Model in ThanoSQL
---

# __사용자의 모델을 ThanoSQL에서 사용하기__

## __0. 데이터 세트 및 모델 준비__

### Beans 데이터 세트

이 데이터 세트는 우간다의 농업 연구를 담당하는 국가 기관인 국립 작물 자원 연구소(NaCRRI)와 협력하여 Makerere AI 연구소가 우간다의 여러 지역에서 현장에서 촬영한 잎 이미지입니다. 데이터는 총 3개의 클래스로 구성되어 있습니다. 2개의 질병 클래스와 건강 클래스이고, 질병은 각각 세균모무늬병(Angular leaf spot)과 콩 녹병(Bean rust)입니다.

홈페이지: <https://github.com/AI-Lab-Makerere/ibean>

### __데이터 세트 준비__

#### 데이터 다운로드 및 압축풀기


```python
import os
from shutil import unpack_archive
from urllib.request import urlretrieve

url = "https://storage.googleapis.com/ibeans"

for split in ["train", "validation", "test"]:
    urlretrieve(f"{url}/{split}.zip", f"{split}.zip")
    unpack_archive(f"{split}.zip", ".")
    os.remove(f"{split}.zip")
```

#### 패키지 설치


```python
!pip install torch torchvision
```

#### 훈련용 데이터 세트 생성

이후의 코드는 해당 [링크](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)에서 가져와 약간의 조정을 거친 것입니다.


```python
from torch.utils.data import DataLoader
from torchvision import transforms as T
from torchvision.datasets import ImageFolder

data_transforms = {
    "train": T.Compose(
        [
            T.RandomResizedCrop(224),
            T.RandomHorizontalFlip(),
            T.ToTensor(),
            T.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
        ]
    ),
    "validation": T.Compose(
        [
            T.Resize(224),
            T.CenterCrop(224),
            T.ToTensor(),
            T.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
        ]
    ),
}

image_datasets = {
    split: ImageFolder(split, data_transforms[split])
    for split in ["train", "validation"]
}
dataloaders = {
    split: DataLoader(image_datasets[split], batch_size=8, shuffle=split == "train")
    for split in ["train", "validation"]
}
dataset_sizes = {split: len(image_datasets[split]) for split in ["train", "validation"]}
```

### __모델 준비__

#### 모델 학습 코드 작성하기


```python
import time
import copy
import torch


def train_model(model, criterion, optimizer, num_epochs=3):
    start_time = time.time()
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    model.to(device)
    best_model_weights = copy.deepcopy(model.state_dict())
    best_acc = 0.0

    for epoch in range(num_epochs):
        print(f"Epoch {epoch}/{num_epochs - 1}")
        print("-" * 10)

        # 각 에폭(epoch)은 학습 단계와 검증 단계를 갖습니다.
        for phase in ["train", "validation"]:
            if phase == "train":
                model.train()
            else:
                model.eval()

            running_loss = 0.0
            running_corrects = 0

            for inputs, labels in dataloaders[phase]:
                inputs = inputs.to(device)
                labels = labels.to(device)

                # 매개변수 경사도를 0으로 설정
                optimizer.zero_grad()

                # 순전파
                # 학습 단계에서만 연산 기록을 추적
                with torch.set_grad_enabled(phase == "train"):
                    outputs = model(inputs)
                    preds = torch.argmax(outputs, dim=1)
                    loss = criterion(outputs, labels)

                    # 학습 단계인 경우에만 역전파
                    if phase == "train":
                        loss.backward()
                        optimizer.step()

                # 통계
                running_loss += loss.item() * inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)

            epoch_loss = running_loss / dataset_sizes[phase]
            epoch_acc = running_corrects / dataset_sizes[phase]

            print(f"{phase} Loss: {epoch_loss:.4f} Acc: {epoch_acc:.4f}")

            # 모델의 정확도가 기존의 최고 정확도보다 높다면 저장
            if phase == "validation" and epoch_acc > best_acc:
                best_acc = epoch_acc
                best_model_weights = copy.deepcopy(model.state_dict())

        print()

    time_elapsed = time.time() - start_time
    print(f"Training complete in {time_elapsed // 60:.0f}m {time_elapsed % 60:.0f}s")
    print(f"Best val Acc: {best_acc:4f}")

    # 가장 나은 모델 가중치를 불러옴
    model.load_state_dict(best_model_weights)
    return model
```

#### 모델 불러오기

mobilevit v2를 사용합니다. 작고 가볍지만 정확도는 높아 빠른 튜토리얼에 적합합니다.


```python
model = torch.hub.load("rwightman/pytorch-image-models", "mobilevitv2_050", pretrained=True, num_classes=3)
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
criterion = torch.nn.CrossEntropyLoss()
```

#### 모델 훈련 및 저장


```python
trained_model = train_model(model, criterion, optimizer, num_epochs=3)
```

    Epoch 0/2
    ----------
    train Loss: 0.5623 Acc: 0.7998
    validation Loss: 0.1255 Acc: 0.9474
    
    Epoch 1/2
    ----------
    train Loss: 0.3426 Acc: 0.8859
    validation Loss: 0.0830 Acc: 0.9774
    
    Epoch 2/2
    ----------
    train Loss: 0.2491 Acc: 0.9023
    validation Loss: 0.0869 Acc: 0.9774
    
    Training complete in 2m 9s
    Best val Acc: 0.977444



```python
torch.save(trained_model, "trained_model.pth")
```

#### ThanoSQL에 입력할 데이터프레임 생성


```python
import numpy as np
import pandas as pd

test_dataset = ImageFolder("test", data_transforms["validation"])

data = np.stack([img.numpy() for img, _ in test_dataset])
df = pd.DataFrame(pd.Series(data.tolist()), columns=["image"])  # column 이름을 "image"로 지정해야 합니다.
df.to_pickle("test_data.pkl")
```

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.


```python
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```


```python
%%thanosql
COPY beans_test 
OPTIONS (overwrite=True)
FROM "test/udm_tutorial/test_data.pkl"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>COPY</strong>" 쿼리 구문을 사용하여 데이터베이스에 저장 할 데이터 세트명을 지정합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>COPY</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite": 동일 이름의 데이터 세트가 데이터베이스 상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT *
FROM beans_test
LIMIT 5
```




<div class="df_size">
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
      <th>image</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[[[-0.028684020042419434, -0.04580877348780632...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[-0.0629335269331932, -0.0629335269331932, -...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[1.9577873945236206, 1.8721636533737183, 1.7...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[0.21106265485286713, 0.0569397434592247, -0...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[-1.3815395832061768, -1.432913899421692, -1...</td>
    </tr>
  </tbody>
</table>
</div>



## __2. 사용자 모델 올리기__

아래의 쿼리문을 실행하고 사용자 모델을 업로드합니다.


```python
%%thanosql
UPLOAD MODEL beans_mobilevit
OPTIONS (
    overwrite=True,
    framework="pytorch"
    )
FROM "test/udm_tutorial/trained_model.pth"
```

    Success


## __3. 사용자 모델을 사용하여 예측__

아래의 쿼리문을 실행하고 사용자 모델을 사용하여 결과를 예측해 봅니다.


```python
%%thanosql
PREDICT
USING beans_mobilevit
AS (
    SELECT *
    FROM beans_test
    ORDER BY RANDOM()
    LIMIT 5
    )
```




<div class="df_size">
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
      <th>image</th>
      <th>predict_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[[[-0.542426586151123, -0.9705454111099243, -1...</td>
      <td>[-2.1083998680114746, -1.6671124696731567, 3.9...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[-0.8335474133491516, -0.6622998714447021, 0...</td>
      <td>[4.959186553955078, -1.3661561012268066, -3.34...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[-0.6280503869056702, -0.6965493559837341, -...</td>
      <td>[-1.100450038909912, -1.4762022495269775, 2.74...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[0.8960527777671814, 0.7761794924736023, 0.8...</td>
      <td>[-0.9654994010925293, -0.5125135183334351, 1.5...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[-0.3026800751686096, -0.5253018736839294, -...</td>
      <td>[-1.5803372859954834, -2.1661438941955566, 3.9...</td>
    </tr>
  </tbody>
</table>
</div>




```python
pred_df = _  # 가장 마지막에 사용된 객체를 불러옵니다.
pred_df["predict_result"] = pred_df["predict_result"].apply(np.argmax)
pred_df["predict_result"] = pred_df["predict_result"].apply(test_dataset.classes.__getitem__)
pred_df
```




<div class="df_size">
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
      <th>image</th>
      <th>predict_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[[[-0.542426586151123, -0.9705454111099243, -1...</td>
      <td>healthy</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[-0.8335474133491516, -0.6622998714447021, 0...</td>
      <td>angular_leaf_spot</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[-0.6280503869056702, -0.6965493559837341, -...</td>
      <td>healthy</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[0.8960527777671814, 0.7761794924736023, 0.8...</td>
      <td>healthy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[-0.3026800751686096, -0.5253018736839294, -...</td>
      <td>healthy</td>
    </tr>
  </tbody>
</table>
</div>


