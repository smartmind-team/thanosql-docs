# __이미지로 유사한 이미지 검색하기__ 

**[이전 문서 - 키워드로 이미지 검색하기](/tutorials/thanosql_search/image_search/select_image_query/)** <br>**[다음 문서 - 텍스트로 원하는 이미지 검색하기](/tutorials/thanosql_search/image_search/clip_image_search/)**

## 시작 전 사전정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 참고문서 : [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/), [A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/abs/2002.05709)
- 마지막 수정날짜 : 2022-06-01

## 튜토리얼 소개

!!! note "이미지 수치화 기술 이해하기"
    이미지는 고차원 데이터(높이x너비x채널[RGB]x색의 강도)로써 각 픽셀의 정보를 무작위로 생성한다면 아무 의미를 갖지 않는 이미지가 생성됩니다. 즉, 각 픽셀은 주변 픽셀과 연관된 특정 패턴을 갖는 경우에만 사람이 이미지로 인식을 할 수 있게 됩니다. 이는 이미지를 실제보다 저차원적 특성 벡터에 표현할 수 있다는 의미입니다. 최근에는 인공지능을 이용해서 각 이미지가 갖는 의미의 유사도에 따라 저차원 공간에 수치화하여 표현하는 연구들이 다방면으로 이루어지고 있으며 이는 이미지 수치화, 벡터화(Vectorization), 임베딩(Embedding) 등의 이름으로  다양하게 활용되고 있습니다. 

이미지의 유사도를 정의하는 방법은 여러가지가 있습니다. 색상이 비슷하거나, 이미지 내의 사물이 비슷하거나, 손글씨처럼 의미가 동일할 수도 있습니다. 유사한 이미지에 대한 정확한 정의를 내리기 어렵지만 이미지가 보유하고 있는 일반적인 특징을 인공지능은 학습하고 수치화합니다.

ThanoSQL에서는 이미지를 입력하고 DB에서 유사한 이미지를 검색하기 위해 [자가학습모델(Self-Supervised Learning Model)](https://en.wikipedia.org/wiki/Self-supervised_learning)을 사용합니다. 사용자가 보유하고 있는 이미지들을 ThanoSQL의 DB에 올리면 인공지능 알고리즘을 통해 비슷한 이미지는 가깝게 다른 이미지들은 멀리 배치하며 스스로 학습을 진행합니다. 정답이 없는 데이터셋에서 이미지의 일반적인 표현을 학습하고 소량의 목표값(Target)이 있는 이미지로 미세 조정하여 분류나 회귀 작업에 활용할 수 있습니다. 

또한, ThanoSQL은 인공지능 알고리즘을 이용해서 데이터 세트를 수치화 합니다. 이렇게 수치화 된 데이터는 DB의 컬럼 내에 저장되고, 이미지 간 유사도(거리) 계산을 통해 비슷한 이미지를 검색하는데 사용됩니다.

__아래는 ThanoSQL 유사 이미지 검색 알고리즘의 활용 및 예시 입니다.__   
- 사용자가 좋아하는 이미지를 입력받으면 DB 상에 저장되어 있는 미술 작품 중에서 비슷한 느낌의 미술 작품들을 검색하고 사용자에게 추천합니다. <br>
- 수천장의 사진이 담겨있는 앨범에서 유사 이미지를 찾아낼 수 있습니다.<br>
- 이미지의 수치화 된 값을 ThanoSQL의 DB에 저장하고, ThanoSQL Auto-ML 회귀/분류 예측 모델을 이용해서 나만의 검색 엔진이나 인공지능 모델을 만들 수 있습니다. <br>


본 튜토리얼에서는 MNIST 손글씨 데이터를 이미지 처리 알고리즘 중 하나인 SimCLR을 사용하여 임베딩 합니다. 후에 임베딩 된 데이터 테이블을 사용하여 비슷한 이미지 검색을 진행합니다. <br>

아래는 ThanoSQL 임베딩 알고리즘의 활용 및 기대효과 입니다.
* 마음에 드는 미술작품의 이미지파일로 비슷한 느낌의 미술작품들을 검색 할 수 있습니다.
* 수천장의 사진이 담겨있는 앨범에서 유사 이미지를 찾아낼수 있습니다.
* 임베딩 된 값을 정형모델을 사용하여 회귀예측, 분류 예측 서비스 구축이 가능합니다.

!!! note "본 튜토리얼에서는" 
    :point_right: 이번 튜토리얼에서는 `MNIST` 손글씨 데이터 세트를 사용합니다. 각 이미지는 0~1 사이의 값을 갖는 고정 크기(28x28 = 784 픽셀) 이며, 여러 사람들이 손으로 쓴 0~9까지 숫자를 정답과 함께 제공합니다. 60,000 개의 학습용 데이터 세트와 10,000개의 테스트용 데이터 세트로 이루어져 있습니다. <br> 
    
ThanoSQL을 사용하여 손글씨 데이터를 입력하고 DB 내에서 입력 이미지와 유사한 이미지를 검색해주는 모델을 만들어 봅니다. 

![MNIST 데이터](/img/thanosql_search/simclr_search/simclr_img7.png) 

## __1. 데이터 세트 확인__

`MNIST` 손글씨 분류 모델을 만들기 위해 ThanoSQL [DB](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 저장되어 있는 `mnist_dataset` 테이블을 사용합니다. `mnist_dataset` 테이블은 `MNIST` 이미지 파일들이 저장되어 있는 경로와 파일 이름 그리고 라벨 정보가 담겨 있는 테이블입니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```sql
%%thanosql
SELECT * 
FROM mnist_dataset 
LIMIT 5
```
![1](/img/thanosql_search/simclr_search/simclr_img1.png) <br>


!!! note "데이터 테이블 이해하기" 
    `mnist_dataset` 테이블은 아래와 같은 정보를 담고 있습니다. "18759.jpg" 이미지 파일은 숫자 0을 쓴 손글씨 이미지이고 "18566.jpg" 파일은 숫자 2를 쓴 손글씨 이미지 파일입니다.

    - `img_path`: 이미지 경로
    - `filename`: 파일이름
    - `label` : 이미지 라벨

## __2. 이미지 수치화 모델 생성__

이전 단계에서 확인한 `mnist_dataset` 데이터를 사용하여 이미지 수치화 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 `mnist_model`이라는 이름의 모델을 만듭니다.

```sql
%%thanosql
BUILD MODEL mnist_model
USING SimCLR
OPTIONS (max_epochs=5)
AS 
SELECT * 
FROM mnist_dataset
```

!!! note "쿼리 세부정보" 
    "__BUILD MODEL__" 쿼리 구문을 사용하여 `mnist_model` 이라는 모델을 만들고 학습시킵니다. <br> "__OPTIONS__"는 모델 생성 및 학습에 필요한 변수값들을 정의합니다. 이미지 수치화를 위해서 `SimCLR`이라는 베이스 모델을 사용합니다. <br> 이미지 수치화 모델을 생성하기 위해서 학습횟수(`max_epochs`), 이미지 칼럼 이름(`image_col`), 파일 이름(`fname`), 그리고 목표값(`target`)이 되는 컬럼의 이름을 적어줍니다.   


아래 쿼리 구문을 사용하여 이미지 수치화 결과를 확인합니다. `mnist_model`을 "__CONVERT__" 쿼리 구문을 사용하여 `mnist_dataset` 이미지들을 임베딩합니다. 

```sql
%%thanosql

CONVERT USING mnist_model
OPTIONS(
    table_name= "mnist_dataset"
    )
AS 
SELECT * 
FROM mnist_dataset

```

![4](/img/thanosql_search/simclr_search/simclr_img3.png) <br>

!!! note "쿼리 세부정보" 
    "__CONVERT USING__" 쿼리 구문은 `mnist_model`을 이미지 수치화를 위한 알고리즘으로 사용합니다. <br> "__OPTIONS__" 쿼리 구문은 이미지 수치화 시 필요한 변수들을 정의합니다. ThanoSQL DB 내에 저장될 테이블 이름("table_name")을 정의합니다. `mnist_dataset` 테이블에 `mnist_model_SimCLR`이라는 컬럼을 새롭게 생성하고 수치화 결과를 저장합니다.

## __3. (이미지 폴더로부터) 이미지 수치화 결과 저장__

아래의 쿼리 구문을 실행하여 특정 폴더 내의 전체 이미지를 기존에 학습해 둔 모델을 사용해서 수치화 합니다. 위의 쿼리와 같은 역할을 하지만 학습을 위한 입력 데이터의 구조가 다릅니다.

```sql
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

!!! note "쿼리 세부정보" 
    "__CREATE TABLEL__" 쿼리 구문을 사용하여 이미지 수치화 테이블(`mnist_embds`)을 만듭니다. <br>
    "__USING__"은 이미지 수치화에 사용할 모델을 정의합니다.<br> 
    "__OPTIONS__"는 이미지 수치화를 위한 이미지 파일의 속성값들을 정의합니다. "path_type"은 데이터가 저장되어 있는 파일 경로의 타입(folder|file), "data_type"은 입력하는 비정형 데이터의  종류(image|audio|video), "file_type"은 대상 파일의 확장자를 적어줍니다. <br>
    "__FROM__" 쿼리 구문을 사용하여 이미지 파일들이 들어있는 폴더의 경로를 적어줍니다.


아래 쿼리 구문을 사용하여 생성한 이미지 수치화 결과 테이블을 확인합니다.
각 이미지들마다 수치화 된 값들이 추가 되어있는 것을 확인할 수 있습니다.

```sql
%%thanosql
SELECT * 
FROM mnist_embds 
LIMIT 5
```

![image](/img/thanosql_search/simclr_search/simclr_img2.png) 

## 4. 이미지 수치화 모델을 사용해서 유사 이미지 검색하기

이번 단계에서는 `mnist_model` 이미지 수치화 모델과 `mnist_embds` 수치화 테이블을 사용하여 "35322.jpg" 이미지 파일(손글씨 6)과 유사한 이미지를 검색합니다. <br>

![image](/img/thanosql_search/simclr_search/simclr_img8.png) 

35322.jpg 이미지파일


```sql
%%thanosql
SEARCH IMAGE images='/data/development-model/data/mnist/MNIST_DATASET/test/6011.jpg' 
USING mnist_model 
AS 
SELECT * 
FROM mnist_embds
```

![5](/img/thanosql_search/simclr_search/simclr_img4.png) <br>


!!! note "쿼리 세부정보" 
    "__SEARCH IMAGE [images|audio|videos]__" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오 파일을 정의합니다.  <br>
    "__USING__"은 이미지 수치화에 사용할 모델을 정의합니다.<br>
    "__AS__" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. `mnist_embds` 테이블을 사용합니다 

다음 쿼리를 실행하여 "__SEARCH__" 결과를 ThanoSQL의 "__PRINT__" 쿼리 구문을 활용하여 가장 유사한 상위 4개를 출력합니다. 학습을 조금 밖에 진행하지 않았지만 6과 비슷한 이미지를 출력하는 것을 확인할 수 있습니다.


```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image 
    AS image, similarity_image1 
    FROM (
        SEARCH IMAGE images='/data/development-model/data/mnist/MNIST_DATASET/train_data/35322.jpg' 
        USING mnist_model 
        AS 
        SELECT * 
        FROM mnist_embds
        )
    ORDER BY similarity_image1 DESC 
    LIMIT 4)
```


![5_1](/img/thanosql_search/simclr_search/simclr_img5.png) <br>
    


## 6. 튜토리얼을 마치며

이번 튜토리얼에서는 `MNIST` 손글씨 데이터 세트를 사용하여 이미지 수치화와 수치화 결과를 바탕으로한 유사 이미지 검색까지 진행해 보았습니다. 이번 튜토리얼에서는 이미지 유사도의 정확도보다는 작동 위주의 설명으로 진행하였습니다. 이미지 수치화 모델은 각 이미지 데이터 세트에 맞는 정밀한 튜닝과 소량의 정답을 학습 시에 추가하여 정확도를 향상 시킬 수 있습니다. 나만의 이미지 수치화 모델을 만들어 다양한 형태의 비정형 데이터 세트에 검색 기능을 추가하고 Auto-ML 기법을 이용한 나만의 모델을 배포할 수 있습니다.
<br>
다음 단계에서 이미지 수치화 모델의 다양한 "__OPTIONS__" 쿼리 구문과 학습 방법을 더욱 심도있게 다뤄봅니다. 나만의 정확한 이미지 변환 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행 해보세요.

* [나만의 데이터 업로드하기](/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 유사 이미지 검색 모델 만들기]()

!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    유사 이미지 검색 모델 구축 관련 문의: contact@smartmind.team