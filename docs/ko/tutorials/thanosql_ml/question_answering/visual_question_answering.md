---
title: 시각 질의 응답 모델 사용하기  
--- 

# __시각 질의 응답 모델 사용하기__

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 5분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_ml/question_answering/visual_question_answering.ipynb
- 참고 문서 : [COCO 데이터 세트](https://cocodataset.org/#download), [VQA](https://visualqa.org/index.html), [ViLT](https://arxiv.org/abs/2102.03334)

## 튜토리얼 소개 

<div class="admonition note">
    <h4 class="admonition-title">시각 질의 응답 이해하기</h4>
    <p>시각 질의 응답은 이미지(Visual)와 그 이미지에 대한 질문(Question)이 주어졌을 때, 해당 질문에 대해 알맞은 답변(Answer)을 찾는 기술입니다. 시각 질의 응답은 어린이 학습, 인공지능 비서 등 여러 분야에 활용할 수 있는 기술입니다.</p>
</div>

[VQA Challenge](https://visualqa.org/challenge.html)는 2016년 CVPR을 시작으로 매년 개최되며, 1년마다 발전된 기술을 평가하고 시상하고 있습니다. 2017년부터는 같은 질문에 비슷한 이미지를 보여주고 다른 답변을 하는 데이터를 VQA 2.0 데이터 세트를 통해 수집한 후 인공지능의 유효성을 엄밀히 평가하고 있습니다.
예를 들어, ‘누가 안경을 쓰고 있나?’라는 질문에 비슷한 이미지가 주어지면 ‘남자’ 또는 ‘여자’의 답을 가질 수 있도록 데이터의 분포를 고려하는 것을 말합니다.

**아래는 ThanoSQL 시각 질의 응답의 활용 및 예시 입니다.**

- 온라인 상품 판매 서비스에서 시각 장애인 보조 시스템으로 활용할 수 있습니다. 상품 이미지를 확인할 수 없는 고객이 해당 상품의 색상, 재질에 대한 질문을 할 때 시각 질의 응답 모델을 활용하여 CS 업무를 줄일 수 있습니다.

- 유사 이미지들에서 필요한 정보를 하나의 질문을 통하여 한 번에 찾을 수 있는 서비스를 만들 수 있습니다.


<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 해당 모델은 예측만 사용할 수 있기 때문에 사전 학습된 모델과 COCO 데이터 세트에서 사람이 있는 이미지들만 사용하여 해당 이미지에 몇 명의 사람이 있는지에 대해 질의를 하고 결과를 확인해 봅니다.</p>
</div>


<div class="admonition tip">
    <h4 class="admonition-title">데이터 세트 설명</h4>
    <p><code>COCO</code> 데이터 세트는 객체 탐지(object detection), 분할(segmentation), 키포인트 탐지(keypoint detection) 등의 목적으로 만들어진 데이터 세트입니다. 본 튜토리얼에서는 해당 데이터 세트에서 2017년 검증 이미지 중 사람 카테고리의 이미지(2,685장)만으로 구성되어 있습니다.</p>
</div>

## __0. 데이터 세트 및 모델 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.


```python
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

### __데이터 세트 준비__


```python
%%thanosql
GET THANOSQL DATASET coco_person_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" 쿼리 구문을 사용하여 원하는 데이터 세트를 워크스페이스에 저장합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>GET THANOSQL DATASET</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY coco_person_data 
OPTIONS (overwrite=True)
FROM 'thanosql-dataset/coco_person_data/coco_person.csv'
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>COPY</strong>" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>COPY</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

### __모델 준비__


```python
%%thanosql
GET THANOSQL MODEL vilt
OPTIONS (overwrite=True)
AS tutorial_vilt
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>GET THANOSQL MODEL</strong>" 쿼리 구문을 사용하여 원하는 모델을 워크스페이스 및 DB에 저장합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>GET THANOSQL MODEL</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" 쿼리 구문을 사용하여 해당 모델의 이름을 지정합니다. AS 구문을 사용하지 않을 경우 <code>THANOSQL MODEL</code>의 이름을 그대로 사용합니다. </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

시각 질의 응답 모델을 사용하기 위해 우리는 ThanoSQL 워크스페이스 DB에 저장되어 있는 `coco_person_data` 테이블을 사용합니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT *
FROM coco_person_data
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
      <th>image_path</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/coco_person_data/000000398905...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/coco_person_data/000000562243...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/coco_person_data/000000376307...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/coco_person_data/000000441586...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/coco_person_data/000000007281...</td>
      <td>person</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
   <h4 class="admonition-title">데이터 이해하기</h4>
   <ul>
      <li><mark style="background-color:#D7D0FF ">image_path</mark>: 각 이미지 파일의 위치 정보</li>
      <li><mark style="background-color:#D7D0FF ">category</mark> : 이미지의 범주</li>
</div>


```python
%%thanosql
PRINT IMAGE 
AS
SELECT image_path 
FROM coco_person_data 
LIMIT 5
```

    /home/jovyan/thanosql-dataset/coco_person_data/000000398905.jpg



    
![jpeg](/img/tutorials/thanosql_ml/visual_question_answering/output_16_1.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000562243.jpg



    
![jpeg](/img/tutorials/thanosql_ml/visual_question_answering/output_16_3.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000376307.jpg



    
![jpeg](/img/tutorials/thanosql_ml/visual_question_answering/output_16_5.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000441586.jpg



    
![jpeg](/img/tutorials/thanosql_ml/visual_question_answering/output_16_7.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000007281.jpg



    
![jpeg](/img/tutorials/thanosql_ml/visual_question_answering/output_16_9.jpg)
    


## __2. 사전 학습된 모델을 사용하여 이미지의 질문에 대한 응답 예측__

다음 쿼리문을 실행하면, 위에서 불러온 사전에 학습을 해 둔 시각 질의 응답 모델, <mark style="background-color:#E9D7FD ">tutorial_vilt</mark>모델을 사용하여 결과를 빠르게 예측해 볼 수 있습니다.


```python
%%thanosql
PREDICT USING tutorial_vilt
OPTIONS (
    image_col='image_path',
    question='How many people are there?',
    column_name='predict_result'
    )
AS
SELECT image_path
FROM coco_person_data
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
      <th>image_path</th>
      <th>predict_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/coco_person_data/000000398905...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/coco_person_data/000000562243...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/coco_person_data/000000376307...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/coco_person_data/000000441586...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/coco_person_data/000000007281...</td>
      <td>10</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2680</th>
      <td>thanosql-dataset/coco_person_data/000000008844...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2681</th>
      <td>thanosql-dataset/coco_person_data/000000321790...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2682</th>
      <td>thanosql-dataset/coco_person_data/000000166478...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2683</th>
      <td>thanosql-dataset/coco_person_data/000000122672...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2684</th>
      <td>thanosql-dataset/coco_person_data/000000163562...</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>2685 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" 쿼리 구문을 통해 이전 단계에서 불러온 <mark style="background-color:#E9D7FD ">tutorial_vilt</mark> 모델을 예측에 사용합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 예측에 사용할 옵션을 지정합니다.
        <ul>
            <li>"image_col" : 예측에 사용할 이미지의 경로가 기록되어 있는 컬럼의 이름 (default: "image_path")</li>
            <li>"question" : 예측에 사용할 질문 내용을 입력합니다.</li>
            <li>"column_name" : 결과를 담을 컬럼의 이름을 정의합니다. (default: "predict_result")</li>
        </ul>
        </li>
    </ul>
</div>

## __3. 튜토리얼을 마치며__

이번 튜토리얼에서는 시각 질의 응답 모델을 사용하여 `COCO 데이터 세트`에서 이미지에 대한 질문을 통해 결과를 예측해 보았습니다. 초급 단계의 튜토리얼인 만큼 간단한 쿼리를 통해 눈에 보이는 결과를 얻는 것 위주로 진행했습니다. 필요한 이미지들에 대해서만 질문을 한다면, 보다 원하는 결과에 가까운 값을 얻을 수 있을 것입니다.

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>시각 질의 응답 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
