# ThanoSQL 무작정 따라하기

## (ThanoSQL 무작정 따라하기) 자기주도학습 임베딩 추출모델을 사용하여 MNIST 손글씨 이미지 분류하기

**[이전 문서 - DB에서 내가 원하는 데이터 검색하기](/tutorials/thanosql_search/image_search/select_image_query/)** <br>**[다음 문서 - 텍스트로 원하는 이미지 검색하기](/tutorials/thanosql_search/image_search/clip_image_search/)**

## 시작 전 사전정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 참고문서 : [MNIST 데이터 세트 페이지](http://yann.lecun.com/exdb/mnist/), [SimCLR공식 논문](https://arxiv.org/abs/2002.05709)
- 마지막 수정날짜 : 2022-06-01

**[이전 문서 - 자동화된 머신러닝을 이용하여 캐글 자전거 수요 예측 회귀 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/tabular/regression/docs/tutorials/thanosql_ml/tabular/regression/lv1_automl_regression_kor.md)** <br>
**| [다음 문서 - Microsoft 뉴스 데이터 세트를 이용한 뉴스 추천 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lda_kor_0_1.md)**

<br>

## 튜토리얼 소개

>__이미지 임베딩 이해하기__ : 텍스트 시스템과 달리, 이미지 처리 시스템은 개별적인 원시 픽셀 강도를 지닌 이미지를 나타내는 풍부하고 고차원적인 데이터세트로 작동합니다. 하지만 원래의 밀도가 높은 형태의 이미지는 일부 작업에 그리 유용하지 않을 수 있습니다. 예를 들어 잡지 표지 이미지를 보고 비슷한 잡지를 찾거나 참조 사진과 비슷한 사진을 찾아야 한다고 가정해보세요. 입력 사진의 원시 픽셀(2,048 ✕ 2,048)을 다른 사진과 비교하여 비슷한지 여부를 찾는 것은 효율적이거나 효과적이지 않습니다. 하지만 이미지의 저차원적 특성 벡터(임베딩)를 추출하면 이미지에 포함된 내용이 무엇인지를 나타내는 일정한 지표를 얻고, 더 효과적으로 비교할 수 있습니다.

>__SimCLR 이해하기__ : 인공지능 모델(ML, DL)을 학습 시키는데에는 크게 정답이 필요한 지도학습과 정답이 필요없는 비지도학습 두가지 방법이 존재합니다. SimCLR은 비지도학습에 속하지만 타 비지도학습 모델들과 다르게 자기주도학습을 합니다. 스스로 이미지를 변형하고 변형된 이미지들의 차이를 대조학습(Contrastive learning)을 통해 비슷한 이미지는 가깝게 다른 이미지들을 멀리 배치하며 학습합니다. 정답 레이블이 없는 데이터셋에서 이미지의 일반적인 표현을 학습하고 소량의 레이블이 있는 이미지로 미세 조정하여 주어진 분류 작업에 대해 우수한 성능을 달성할 수 있습니다.    

본 튜토리얼에서는 MNIST 손글씨 데이터를 이미지 처리 알고리즘 중 하나인 SimCLR을 사용하여 임베딩 합니다. 후에 임베딩 된 데이터 테이블을 사용하여 비슷한 이미지 검색을 진행합니다. <br>

아래는 ThanoSQL 임베딩 알고리즘의 활용 및 기대효과 입니다.
* 마음에 드는 미술작품의 이미지파일로 비슷한 느낌의 미술작품들을 검색 할 수 있습니다.
* 수천장의 사진이 담겨있는 앨범에서 유사 이미지를 찾아낼수 있습니다.
* 임베딩 된 값을 정형모델을 사용하여 회귀예측, 분류 예측 서비스 구축이 가능합니다.

:point_right: 이번 튜토리얼에서는 `MNIST` 손글씨 데이터 세트를 사용합니다. `MNIST` 손글씨 데이터 세트는 0부터 9까지 여러 사람들의 손글씨를 모아놓은 데이터 세트입니다. <br>
ThanoSQL을 사용하여 간단하게 손글씨를 분류할 수 있는 숫자 손글씨 분류 모델을 만듭니다.


![MNIST 데이터](/img/thanosql_search/simclr_search/simclr_img7.png) <br> <MNIST 손글씨 데이터>

## 1. 데이터 확인

MNIST 손글씨 분류 모델을 만들기 위해 ThanoSQL [데이터베이스(DB)](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 저장되어 있는 `mnist_dataset` 테이블을 사용합니다. `mnist_dataset` 테이블은 `MNIST` 이미지 파일들이 저장되어 있는 경로와 파일이름 그리고 라벨 정보가 담겨 있는 테이블입니다. 
아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT * FROM mnist_dataset LIMIT 5
```
![1](/img/thanosql_search/simclr_search/simclr_img1.png) <br>


> __데이터 테이블 이해하기 :__ <br>
`mnist` 테이블은 `img_path`(이미지 경로), `filename`(파일이름), `label`(이미지 라벨)에 대한 정보를 담고 있습니다. '18759.jpg' 이미지 파일은 숫자 0을 쓴 손글씨 이미지이고 18566.jpg 파일은 숫자 2를 쓴 손글씨 이미지 파일입니다.

## 2. BUILD MODEL 구문 사용하여 이미지 임베딩 추출 모델 만들기

이전 단계에서 확인한 `MNIST` 데이터를 사용하여 손글씨 임베딩 추출 모델을 만듭니다. 다음 쿼리 구문을 실행하여 `mnist_model` 이름의 이미지 임베딩 추출 모델을 만듭니다.  


```python
%%thanosql
BUILD MODEL mnist_model
USING SimCLR
OPTIONS (max_epochs=5)
AS select * from mnist_dataset
```





> **쿼리 세부정보 :** <br>
>"__BUILD MODEL__" 쿼리 구문을 사용하여 mnist_model 이라는 모델을 만들고 학습시킵니다. <br>
>"__OPTIONS__"는 모델 생성 및 학습에 필요한 변수값들을 정의합니다. SimCLR 임베딩 모델 생성에는 학습횟수(`max_epochs`), 이미지 칼럼 이름(`image_col`), 파일 이름(`fname`), 그리고 타겟값(`target`) 변수 정의가 필요합니다.   

## 3. CREATE TABLE 쿼리 구문을 사용하여 폴더경로를 사용하여 임베딩하기

이번 단계에서는 이전 [1단계](#1)에서 확인하였던 이미지들의 경로가 저장되어 있는 파일이 존재하지 않을때 폴더 경로를 사용하여 임베딩 하는 방법을 알아봅니다.


```python
%%thanosql
CREATE TABLE mnist_embds
USING mnist_model 
OPTIONS(
        path_type='folder', 
        data_type='image',
        file_type=['.jpg']
        ) 
FROM '/data/development-model/data/mnist/MNIST_DATASET/train_data/'
```





> **쿼리 세부정보 :** <br>
>"__CREATE TABLEL__" 쿼리 구문을 사용하여 mnist_embds 이라는 임베딩 결과 테이블을 만듭니다. <br>
>"__USING__"은 이미지 임베딩에 사용할 모델을 정의합니다.<br>
>"__OPTIONS__"는 임베딩을 위한 이미지파일의 속성값들 path_type = 'folder'|'file' , data_type = 'image'|'audio'|'video', file_type = [파일 확장자]) 정의합니다. <br>
>"__FROM__" 이미지 파일들이 들어있는 폴더의 경로를 정의합니다.

## 3.1 임베딩 결과 테이블 확인하기

아래 쿼리 구문을 사용하여 [3단계](#3)에서 생성한 임베딩 결과 테이블을 확인합니다.
각 이미지들마다 임베딩 값들이 추가 되어있는 것을 확인할 수 있습니다.


```python
%%thanosql
SELECT * FROM mnist_embds limit 5
```


![image](/img/thanosql_search/simclr_search/simclr_img2.png) 

## 4. CONVERT 쿼리구문 사용하여 이미지 파일 임베딩 추출하기

이번 단계에서는 이전 단계에서 생성한 `mnist_model`을 CONVERT 쿼리 구문을 사용하여 `mnist_dataset` 이미지들을 임베딩합니다. 


```python
%%thanosql
SELECT img_path, filename, label, mnist_model_SimCLR FROM(
CONVERT USING mnist_model
OPTIONS(table_name= "mnist_embds")
AS SELECT * FROM mnist_embds)
```

![4](/img/thanosql_search/simclr_search/simclr_img3.png) <br>




> **쿼리 세부정보 :** <br>
>"__CONVERT USING__" 쿼리 구문은 [2단계](#2)에서 만든 mnist_model을 임베딩에 사용시킵니다. <br>
>"__OPTIONS__" 쿼리구문은 임베딩시 필요한 변수들을 정의합니다. ThanoSQL 데이터베이스상에 저장될 테이블 이름(table_name)을 정의합니다. 이번 튜토리얼에서는 테이블 이름을 "mnist_embds"로 저장합니다.

## 5. SEARCH 쿼리 구문을 사용하여 유사 이미지 검색하기

이번 단계에서는 이전 [2단계](#2)에서 만들었던 `mnist_model` 임베딩 모델과 [3단계](#3)에서 만들었던 `mnist_embds` 임베딩 테이블을 사용하여 6011.jpg 이미지 파일(손글씨 6)과 유사한 이미지를 검색합니다. <br>

![image.png](attachment:90f16b94-67cf-436f-9cf0-97bfa0690d45.png) 
6011.jpg 이미지파일


```python
%%thanosql
SEARCH IMAGE images='/data/development-model/data/mnist/MNIST_DATASET/test/6011.jpg' 
USING mnist_model 
AS SELECT * FROM mnist_embds
```


![5](/img/thanosql_search/simclr_search/simclr_img4.png) <br>



> **쿼리 세부정보 :** <br>
>"__SEARCH IMAGE [images|audio|videos]__" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오 파일을 정의합니다.  <br>
>"__USING__"은 이미지 임베딩에 사용할 모델을 정의합니다.<br>
>"__AS__" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. 이전 [3단계](#3)에서 만들었던 임베딩 테이블`mnist_embds` 사용합니다 

## 5.1 PRINT 쿼리 구문을 활용하여 SEARCH 결과 바로 출력하기 

다음 쿼리를 실행하여 이전 [5단계](#5)에서 나온 SEARCH 결과를 ThanoSQL의 PRINT 구문을 활용하여 가장 유사한 상위 3개를 출력합니다. 학습을 조금 밖에 진행하지 않았음에도 불구하고 어느정도 6과 비슷한 이미지를 출력하는 것을 확인할 수 있습니다.


```python
%%thanosql
PRINT IMAGE AS
    (SELECT image as image, similarity_image1 FROM
        (SEARCH IMAGE images='/data/development-model/data/mnist/MNIST_DATASET/train_data/35322.jpg' 
        USING mnist_model 
        AS SELECT * FROM mnist_embds)
    ORDER BY similarity_image1 DESC LIMIT 4)
```


![5_1](/img/thanosql_search/simclr_search/simclr_img5.png) <br>
    


## 6. 튜토리얼을 마치며

이번 튜토리얼에서는 MNIST 손글씨 데이터세트를 사용하여 이미지 임베딩과 임베딩 결과를 바탕으로한 유사 이미지 검색까지 진행해 보았습니다. 이번 튜토리얼에서는 임베딩의 정확도보다는 작동위주의 설명으로 진행하였습니다. SimCLR을 사용한 자기주도학습 모델은 추가되는 이미지 데이터 세트에 맞는 정밀한 튜닝과 소량의 정답을 학습시에 추가하여 정확도를 향상 시킬 수 있습니다. 나만의 임베딩 모델을 만들어 다양한 형태의 비정형 데이터세트를 정형 데이터로 만들고 자동화 된 머신러닝(Auto-ML) 기법을 이용한 배포 또한 가능합니다.   
<br>
다음 단계인 [중급 자기주도학습 임베딩 모델 만들기]() 튜토리얼에서는 임베딩 모델을 더욱 심도있게 다뤄봅니다. 나만의 정확한 이미지 변환 임베딩 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행 해보세요.

* [나만의 데이터 업로드하기]()
* [중급 자기주도학습 임베딩 모델 만들기]()

## 7. 나만의 서비스를 위한 모델 배포 관련 문의

ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요 :smile:  <br>
임베딩 모델 구축 관련 문의: contact@smartmind.team