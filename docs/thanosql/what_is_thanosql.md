




# ThanoSQL은 무엇인가요?

ThanoSQL은 표준 SQL 쿼리를 활용하여 ThanoSQL에서 정형/비정형 데이터를 질의하고 쿼리를 사용하여 모델을 빌드할 수 있게 합니다. ThanoSQL을 통해 다수의 데이터 타입과 언어, 프레임 워크를 사용할 필요가 없어 개발 속도가 향상됩니다.

다음을 통해 ThanoSQL을 사용할 수 있습니다. 
* Jupyter + Python
* Rest API

정형, 비정형 데이터 세트를 활용하여 대규모 머신러닝을 진행할 시에는 다양한 언어, 프레임 워크, 람다 아키텍처 기반의 빅데이터 플랫폼 등 광범위한 지식과 기술 구현이 필요합니다. 

이러한 복잡한 요구 사항들로 인한 기술 구현의 난이도와 복잡성이 점진적으로 증가하여 개발 기간과 인력, 운영 개발/배포 등에 많은 시간과 비용을 할애하게 되는 문제점이 발생합니다.

ThanoSQL을 사용하면 데이터 사이언티스트가 표준 SQL을 통해 정형, 비정형 데이터 모두를 질의할 수 있으며 실시간으로 누적되는 Ops table로부터 
Sample data를 만들어 Modeling하고 빠르게 실험할 수 있습니다. 

![Group 8623](https://user-images.githubusercontent.com/105042667/169228582-8dc29db3-7212-47bf-a3fe-0397db70fc38.png)

데이터 사이언티스트는 ThanoSQL을 통해 Insert 와 Predict 두 라인으로 배포/개발/운영 가능합니다.


# ThanoSQL에서 지원되는 데이터 타입

ThanoSQL은 다음과 같은 데이터 타입 질의를 지원합니다.

* 정형 데이터
* 비정형 데이터
    * 이미지
      * 키워드로 이미지 찾기
      * 이미지로 이미지 찾기
    * 오디오 (업데이트 예정)
    * 비디오 (업데이트 예정)

# ThanoSQL에서 지원되는 모델

ThanoSQL의 모델은 ThanoSQL System이 Sample 데이터에서 학습한 내용을 나타냅니다.

ThanoSQL ML 다음과 같은 유형을 지원합니다.

* Tabular
    * Regression
    * Classification
    * Recommendation
      * 아이템 추천
      * 유저 추천
* Text
    * Classification
* Image
    * Classification
* Audio
    * Recognition
* Video        



# ThanoSQL의 장점

ThanoSQL에는 다음과 같은 장점이 있습니다. 

* ThanoSQL은 표준 SQL을 통해 정형, 비정형 데이터 모두를 질의할 수 있습니다. 이는 사용자가 기본 데이터 웨어하우스 만으로도 실시간으로 누적되는 Ops table을 빠르게 빌드할 수 있도록 합니다.

* ThanoSQL은 운영계와 개발계 구분이 필요가 없으며 모수 테이블에서 실시간으로 Insert가 가능합니다. 

* 비정형 데이터를 처리하기 위해 복잡한 람다 아키텍처 기반의 플랫폼을 빌드할 필요가 없습니다.
이는 시간과 운용인력에 대한 부담을 줄여줍니다.

* 기존에는 개발계 샘플테이블에서 모델링하므로 실시간 데이터 반영 불가능 했습니다. ThanoSQL을 사용하면 실시간성이 보장되는 Ops table로부터 Sample data를 만들어 한곳에서 빠르게 Modeling하고 이를 실험할 수 있습니다. 

* ThanoSQL을 사용하는 데이터 사이언티스트 한명이 응용프로그램에서 바로 Insert와 Predict이 가능합니다.
이는 개발과 배포가 같아져 다양한 정보를 융복합 Insight를 빠르게 만들어 실험할 수 있는 환경을 제공하므로 모델 개발과 비즈니스 혁신 속도를 향상시킬 수 있습니다. 
# ThanoSQL Tutorial Guide v1
![Group 3870](https://user-images.githubusercontent.com/105042667/169217615-92634f84-52c2-4176-a71a-18cdb7db533f.png)

# Upcoming updates

ThanoSQL v1.1 

* 비정형 데이터 처리
    * 오디오 (업데이트 예정)
    * 비디오 (업데이트 예정)
