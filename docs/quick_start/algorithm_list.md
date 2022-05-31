# __안내 가이드__ 

**[이전 문서 - ThanoSQL 웹 사용법](/quick_start/how_to_use_ThanoSQL/)** <br>**[다음 문서 - 키워드로 이미지 검색하기](/tutorials/thanosql_search/image_search/select_image_query/)**

## 시작하기 전에

ThanoSQL은 PostgreSQL을 기반으로 만들어졌으며, SQL 쿼리를 사용하여 머신러닝과 딥러닝 모델을 만들고 실행할 수 있다는 특징을 가지고 있습니다. 이를 통해, 람다 아키텍쳐 기반의 비정형 빅데이터 플랫폼을 ThanoSQL 하나로 대체하여 개발, 배포 그리고 운영을 가능하게 하는 것을 주요 목표로 하고 있습니다.

!!! note "PostgreSQL이란?"
    PostgreSQL은 대용량의 복잡한 트랜잭션 처리를 위해 개발된 RDBMS입니다. PostgreSQL은 객체-관계형 데이터베이스 시스템(ORDBMS)으로, 과거 IBM에서 작성된 RDBMS의 화이트페이퍼를 기반으로 Oracle, DB2 그리고 PostgreSQL이 구현되었기 때문에 MySQL이나 mariaDB와 같은 오픈소스 제품과 달리 PostgreSQL의 경우 상용 RDBMS 급의 기능을 제공하는 오픈소스 DBMS입니다. 오픈소스라는 특성으로, 북미와 일본에서 인지도를 넓히고 있습니다. 

이 가이드에서는 ThanoSQL이 제공하는 인공지능 알고리즘의 종류, 샘플 데이터 세트 그리고 ThanoSQL의 기능에 대해 소개합니다. 

## __1. ThanoSQL에서 제공하는 인공지능 알고리즘__ 

ThanoSQL의 인공지능 알고리즘은 머신러닝과 딥러닝 시스템이 학습 데이터에서 학습한 내용을 나타내며 아래와 같은 모델 유형을 지원합니다. 

- 분류 작업을 위한  Auto-ML(자동화된 머신러닝). 
- 예측 작업을 위한 Auto-ML(자동화된 머신러닝).
- 이미지 분류을 위한 ConvNet. 
- 텍스트 분류을 위한 ELECTRA.
- 음성 인식을 위한 wav2vec.
- 제품 추천 시스템을 위한 LFM. 
- 키워드를 통한 이미지 검색을 위한 CLIP. 
- 이미지 유사도 기반 검색을 위한 SimCLR. 

!!!note ""
    추가로 제공하는 알고리즘은 [모든 안내 가이드](/quick_start/how_to_use_ThanoSQL/)

## __2. ThanoSQL에서 제공하는 샘플 데이터 세트__ 

ThanoSQL의 인공지능 알고리즘을 적용해보기 위해 아래와 같은 샘플 데이터 세트를 지원합니다.  

- [(캐글) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)    
- [(캐글) Bike Sharing Demand](https://www.kaggle.com/competitions/bike-sharing-demand/overview)  
- [(캐글) Cat and Dog 데이터 세트](https://www.kaggle.com/datasets/tongpython/cat-and-dog)    
- [(캐글) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data)  
- [LibriSpeech 데이터 세트](http://www.openslr.org/12)  
- [Movielens 데이터 세트](https://grouplens.org/datasets/movielens/)  
- [Unsplash 데이터 세트](https://unsplash.com/data)  
- [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/)

## __3. ThanoSQL의 쿼리 구문__ 

쿼리 문은 표현식을 한 개 이상 검색하고 계산된 결과 테이블을 반환합니다. ThanoSQL은 아래와 같은 쿼리에 관한 구문을 제공합니다. 

- "__BUILD MODEL__" : ThanoSQL을 이용하여 모델을 학습시킵니다. 
- "__FIT MODEL__" : ThanoSQL 모델을 재학습시킵니다. 
- "__EVALUATE USING__" : 학습한 ThanoSQL 모델을 평가합니다.
- "__TRANSFORM USING__" : 예측을 위해, 학습한 ThanoSQL 모델을 통해 전처리합니다.
- "__PREDICT USING__" : 학습한 ThanoSQL 모델을 이용하여 예측을 진행합니다.
- "__DELETE MODEL__" : 모델 관리를 위해, 학습한 ThanoSQL 모델을 삭제합니다.
- "__LIST__" : ThanoSQL에 저장한 모델을 조회합니다. 
- "__CREATE TABLE__" : 이미지, 음성 그리고 비디오 파일 형태의 비정형 데이터를 수치화하여 데이터 테이블을 생성합니다. 
- "__CONVERT USING__" : 이미지, 음성 그리고 비디오 파일 형태의 비정형 데이터의 정보를 수치화하고 사용할 데이터 테이블에 추가합니다. 
- "__SEARCH__" : 이미지, 음성 그리고 비디오 파일 형태의 비정형 데이터를 검색합니다. 
- "__PRINT__" : 이미지, 음성 그리고 비디오 파일 형태의 비정형 데이터를 출력합니다. 
