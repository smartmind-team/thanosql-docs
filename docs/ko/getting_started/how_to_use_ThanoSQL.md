---
title: ThanoSQL 웹 사용법
---

# __ThanoSQL 웹 사용법__

## __1. [ThanoSQL Web](https://www.thanosql.ai/){style="color:#604bcc" target="_blank"} 접속하기__

ThanoSQL에 접속 후, **Login 버튼(또는 메인화면의 시작하기 버튼)**을 클릭해 로그인 화면으로 접속합니다.

[![IMAGE](/ko/img/getting_started/img0.png)](/ko/img/getting_started/img0.png)

!!! note ""
      ThanoSQL 콘솔을 사용 하기 위해서는 필수로 회원가입을 진행해야 합니다.


## __2. 회원가입__

ThanoSQL 로그인 화면에서 **회원가입 하기 버튼**을 클릭합니다.

[![IMAGE](/ko/img/getting_started/img1.png)](/ko/img/getting_started/img1.png)

- 정보입력 후 **계정 만들기 버튼**을 클릭해 회원가입을 완료합니다.
- **이메일 인증**, **서비스 이용약관**과 **개인정보처리방침**에 동의 후 가입 할 수 있습니다.

[![IMAGE](/ko/img/getting_started/img2.png)](/ko/img/getting_started/img2.png)

## __3. 워크스페이스 생성__

- 로그인 후, **시작하기 버튼**을 클릭해 워크스페이스를 생성 합니다.

[![IMAGE](/ko/img/getting_started/img3.png)](/ko/img/getting_started/img3.png)

## __3-1. 플랜 설정__

[![IMAGE](/ko/img/getting_started/img4.png)](/ko/img/getting_started/img4.png)

① **Plan**

- 원하는 플랜을 선택합니다.
- 시작하기 버튼을 클릭해 다음 단계로 넘어갑니다.

② **Promotion Code**

- 프로모션 코드를 입력합니다.
- 입력 후, 시작하기 버튼을 클릭해 다음 단계로 넘어갑니다.
!!! warning
      프로모션 코드는 일회용입니다. 재사용 할 수 없습니다.

## __3-2. 신청서 작성__

[![IMAGE](/ko/img/getting_started/img5.png)](/ko/img/getting_started/img5.png)

① 워크스페이스 이름을 **입력**합니다.

- 워크스페이스 이름을 중복으로 사용할 수 없습니다.
- 워크스페이스 이름은 **영문(앞자리,소문자)**과 **숫자(뒷자리)**로만 작성 가능합니다. ex)myworkspace123

② 신청 Plan

1. 신청 Plan 확인
      - 신청한 티어, 플랜 분류, 요금제 가격을 확인합니다.
2. 이메일 입력
      - 연락 받을 이메일을 입력합니다.
      - **필수 입력 사항 입니다.**
3. 신청자 성명
      - 신청자의 성명을 입력합니다.
4. 휴대폰 번호
      - 신청자의 휴대폰 번호를 입력합니다.
5. 문의사항
      - 문의사항을 입력해주세요
!!! tip ""
      워크스페이스 사용기한 등 추가 요청 사항을 입력해주세요.

③ 신청 완료

- **완료하기 버튼**을 클릭해 워크스페이스 생성을 요청 / 완료합니다.

## __4. 콘솔 사용__

① 워크스페이스

- 열기 버튼을 클릭해 해당 워크스페이스로 접속할 수 있습니다.
- 워크스페이스 설정에서 SSH Key 다운로드, API Key 복사 / 재발급, 워크스페이스 시작 / 정지, 요금제 변경 등 정보를 제공합니다.

② 추가 워크스페이스 생성

- 워크스페이스 생성 페이지로 이동합니다.

[![IMAGE](/ko/img/getting_started/img6.png)](/ko/img/getting_started/img6.png)

## __5. ThanoSQL 워크스페이스__

정형과 비정형 데이터 모두 SQL 만으로 쿼리(Query, 질의)와 AI(인공지능) 모델링을 할 수 있습니다.

① API 토큰 발급

- 해당 서비스를 이용하기 위해서는 API 토큰을 사용해야 합니다. **GET API_TOKEN 버튼**을 클릭해 API 토큰을 발급 받습니다.(클립보드에 복사) 아래의 쿼리를 통해 정상적으로 서비스를 사용할 수 있습니다.
```sql
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```
- 워크 스페이스에서 ThanoSQL 문법을 사용하기 위해서는 **파일 상단**에서 항상 위 **쿼리**를 실행해야 합니다.

② 워크스페이스 이동

- 다른 워크스페이스(생성한 워크스페이스)로 손쉽게 이동할 수 있습니다.

[![IMAGE](/ko/img/getting_started/img7.png)](/ko/img/getting_started/img7.png)

## __6. 워크스페이스 설정__

워크스페이스의 설정을 확인 및 수정을 할 수 있습니다.

## __6-1. General__

[![IMAGE](/ko/img/getting_started/img10.png)](/ko/img/getting_started/img10.png)

① Workspace Start/Stop

- 워크스페이스 전원을 Start/Stop 동작할 수 있습니다. 요청이 완료 될 때까지 새로운 요청을 보낼 수 없습니다.

② Plan

- 워크스페이스의 Plan 정보를 확인 후 변경을 할 수 있습니다.

③ Workspace 삭제

- 워크스페이스를 삭제할 수 있습니다.

!!! warning
      삭제한 Workspace의 데이터는 완전히 삭제됩니다. 삭제 된 데이터는 복구할 수 없습니다.

## __6.2 Log__

[![IMAGE](/ko/img/getting_started/img11.png)](/ko/img/getting_started/img11.png)

① Workspace Usage

- 워크스페이스 사용 시간을 그래프로 확인 할 수 있습니다.

② Workspace Start/Stop

- 해당 워크스페이스의 최신 로그를 바로 확인할 수 있습니다. **View All 버튼**을 누르면 모든 로그를 확인할 수 있는 페이지로 이동합니다.

## __6.3 Developer__

[![IMAGE](/ko/img/getting_started/img12.png)](/ko/img/getting_started/img12.png)

① API 토큰 발급

- 해당 서비스를 이용하기 위해서는 API 토큰을 사용해야 합니다. API 토큰 발급 버튼을 누를면 클립보드에 복사됩니다. 

② API 토큰 재발급

- API 토큰은 새롭게 발급은 가능하나 이전에 발급받은 토큰은 더 이상 사용할 수 없습니다. API 토큰 재발급 요청 후 API 토큰을 발급 받으면 새로운 토큰을 받을 수 있습니다.

③ SSH Key Download

- SSH 키를 key.pem 파일로 다운로드 받을 수 있습니다.
