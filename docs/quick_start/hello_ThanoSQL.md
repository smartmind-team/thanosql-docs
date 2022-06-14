# __ThanoSQL 워크스페이스 사용법__ 

**[이전 문서 - ThanoSQL 웹 사용법](/quick_start/how_to_use_ThanoSQL/)** <br>**[다음 문서 - 안내 가이드](/quick_start/algorithm_list/)**

## __Hello ThanoSQL!__

ThanoSQL은 정형과 비정형 데이터 모두 SQL 만으로 쿼리(Query, 질의)와 AI(인공지능) 모델링이 가능한 통합 플랫폼입니다. RDB(Relational DataBase, 관계형 데이터베이스), AI 그리고 BIg Data Platform의 기능을 하나의 플랫폼에서 적용할 수 있으며, AI 기반의 디지털 전환시 발생하는 비효율성을 획기적으로 줄일 수 있습니다. 

!!! summary "" 
    - ThanoSQL은 정형과 비정형 데이터 모두 SQL만으로 쿼리가 가능하며, 빠른 AI 모델링을 가능하게 합니다. 
    - 람다 아키텍쳐 기반의 비정형 빅데이터 플랫폼을 ThanoSQL 하나로 대체하여 개발, 배포 그리고 운영을 하나의 프로세스로 가능하게 합니다. 
    - 빅데이터 처리 및 분선 병렬처리 기술을 기반으로 기존 대비 2배 이상 빠르게 데이터 처리를 가능하게 합니다. 

## __ThanoSQL 워크스페이스 사용법__ 

ThanoSQL의 워크스페이스는 [Jupyter Lab](https://github.com/jupyterlab/jupyterlab)을 기반으로 하는 웹 기반 컴퓨팅 환경입니다.

이 환경에서 **ThanoSQL**을 본격적으로 사용하기 위해서는 먼저 **thanosql** cell magic을 불러와야 합니다.

!!! tip ""
    상단의 실행 버튼을 누르거나, **Ctrl + Enter** 혹은 **Shift + Enter** 단축키로도 실행할 수 있습니다.

```sql
%load_ext thanosql
```
다음으로, 각 사용자의 워크스페이스 API_TOKEN 설정을 위해 브라우저 상단의 **GET API_TOKEN** 버튼을 누른 후 붙여넣기하여 설정해줍니다. 

```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>

예시)
%thanosql API_TOKEN=eyJhbGciOiJIUzI1NiIsInR..
```

**ThanoSQL**을 사용할 모든 준비가 끝났습니다.

아래 ThanoSQL문을 실행시키면 Pre-built된 ThanoSQL 모델 목록을 확인 할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL MODEL
```

![image](/img/how_to_use_ThanoSQL/img6.png)

더 많은 ThanoSQL 사용법과 Tutorial은 [ThanoSQL 기술 문서](https://docs.thanosql.ai)에서 확인하세요.