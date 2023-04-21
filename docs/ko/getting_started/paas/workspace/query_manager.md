---
title: Query Manager
---

# __Query Manager 사용법__

Query Manager는 ThanoSQL 명령을 실행하고 결과를 분석할 수 있는 도구입니다. 중요 기능은 다음과 같습니다.

- ThanoSQL 및 기본 SQL 명령 실행
- 데이터 테이블의 '테이블 스키마' / '미리보기'

## __1. 새 쿼리 생성__

- **새 쿼리 만들기** 버튼을 클릭해 쿼리 탭을 생성합니다.

[![IMAGE](/ko/img/getting_started/paas/workspace/img1.png){: style="max-height:none"}](/ko/img/getting_started/paas/workspace/img1.png)

## __2. 쿼리 실행__

- 쿼리를 작성하고 실행해 결과를 볼 수 있습니다.

[![IMAGE](/ko/img/getting_started/paas/workspace/img2.png){: style="max-height:none"}](/ko/img/getting_started/paas/workspace/img2.png)

① 쿼리 입력창

- ThanoSQL 쿼리를 작성합니다.

② 쿼리 실행 버튼

- 쿼리를 실행합니다.

③ 쿼리 실행 결과창

- 쿼리 실행 결과과 테이블 형태로 출력됩니다.

④ 페이지 기능

- 한 번에 표시되는 행 수 조절이 가능합니다. (최대 100행)
- 다음/이전 페이지로 이동 가능하며, 현재 페이지 번호를 알 수 있습니다.


## __3. 데이터 테이블 정보창__

좌측의 **데이터베이스 내비게이션**으로 [스키마](https://www.postgresql.org/docs/current/ddl-schemas.html) 별 테이블 구성 정보를 알 수 있습니다. ThanoSQL에서는 `public`과 `qm` 스키마가 기본적으로 제공되며, Query Manager에서 실행한 쿼리 결과들은 `qm` 스키마에 임의의 테이블 이름으로 저장됩니다. 이를 이용하여 이전 테이블 실행 결과들을 언제든지 재사용할 수 있습니다.

또한, 테이블 명을 클릭하면 다음과 같은 정보를 열람 할 수 있습니다.

### __3-1. 테이블 스키마__

해당 데이터 테이블의 컬럼명, 데이터 타입, Null 가능 여부를 확인 할 수 있습니다.

[![IMAGE](/ko/img/getting_started/paas/workspace/img3.png){: style="max-height:none"}](/ko/img/getting_started/paas/workspace/img3.png)

### __3-2. 미리보기__

해당 데이터 테이블의 내용을 빠르게 파악할 수 있습니다.

!!! warning 
    미리보기에서는 데이터 테이블 편집이 불가능합니다.

[![IMAGE](/ko/img/getting_started/paas/workspace/img4.png){: style="max-height:none"}](/ko/img/getting_started/paas/workspace/img4.png)