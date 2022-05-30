# __안내 가이드__ 

**[이전 문서 - ThanoSQL 웹 사용법](/quick_start/how_to_use_ThanoSQL/)** <br>**[다음 문서 - DB에서 내가 원하는 데이터 검색하기](/tutorials/thanosql_search/image_search/select_image_query/)**

## 시작하기 전에

ThanoSQL은 PostgreSQL을 기반으로 만들어졌으며, SQL 쿼리를 사용하여 머신러닝과 딥러닝 모델을 만들고 실행할 수 있다는 특징을 가지고 있습니다. 이를 통해, 람다 아키텍쳐 기반의 비정형 빅데이터 플랫폼을 ThanoSQL 하나로 대체하여 개발, 배포 그리고 운영을 가능하게 하는 것을 주요 목표로 하고 있습니다.

!!! note "PostgreSQL은 무엇인가?"
    `PostgreSQL`은 대용량의 복잡한 트랜잭션 처리를 위해 개발된 RDBMS입니다. PostgreSQL은 객체-관계형 데이터베이스 시스템(ORDBMS)으로, 과거 IBM에서 작성된 RDBMS의 화이트페이퍼를 기반으로 Oracle, DB2 그리고 PostgreSQL이 구현되었기 때문에 MySQL이나 mariaDB와 같은 오픈소스 제품과 달리 PostgreSQL의 경우 상용 RDBMS 급의 기능을 제공하는 오픈소스 DBMS입니다. 오픈소스라는 특성으로, 북미와 일본에서 인지도를 넓히고 있습니다. 

이 가이드에서는 ThanoSQL이 제공하는 인공지능 알고리즘의 종류, 샘플 데이터 세트 그리고 ThanoSQL의 기능에 대해 소개합니다. 

## __1. ThanoSQL에서 제공하는 인공지능 알고리즘__ 

ThanoSQL의 인공지능 알고리즘은 머신러닝과 딥러닝 시스템이 학습 데이터에서 학습한 내용을 나타내며 아래와 같은 모델 유형을 지원합니다. 

- 분류용 Auto-ML(자동화된 머신러닝). 
- 예측용 Auto-ML(자동화된 머신러닝).
- 음성 인식용 wav2vec.
- 이미지 분류용 ConvNet. 
- 텍스트 분류용 ELECTRA.
- 제품 추천 시스템을 위한 LFM. 
- 키워드를 통한 이미지 검색을 위한 CLIP. 
- 이미지 유사도 기반 검색을 위한 SimCLR. 

## __2. ThanoSQL에서 제공하는 샘플 데이터 세트__ 

ThanoSQL의 인공지능 알고리즘을 적용해보기 위해 아래와 같은 샘플 데이터 세트를 지원합니다.  

- [(캐글) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)    
- [(캐글) Bike Sharing Demand](https://www.kaggle.com/competitions/bike-sharing-demand/overview)  
- [LibriSpeech 데이터 세트](http://www.openslr.org/12)  
- [(캐글) Cat and Dog 데이터 세트](https://www.kaggle.com/datasets/tongpython/cat-and-dog)    
- [(캐글) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data)  
- [Movielens 데이터 세트 페이지](https://grouplens.org/datasets/movielens/)  
- [Unsplash dataset](https://unsplash.com/data)  
- [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/)

## __3. ThanoSQL의 기능__ 




안내 가이드 -"Postgre SQL 문법을 기반으로 만들어 졌습니다", 아래는 설명에 들어갈 내용. 많다면 분류 - 조선근 
[Quick Start] 간략한 설명 > 모델설명 + 알고설명 +데이터세트 설명 + 기능리스트(ThanoSQL 모델다루기 기능) 
예제폴더/파일구성(기본 초기 상태에서 파일 구조, ipynb, 데이터베이스, 모델, 데이터세트) 설명 + 튜토리얼/tech-doc 구성 소개 [Syntax, 무작정 따라하기(초급) 구성 (향후 중급/고급 설명), 독자 대상 등]