# __ThanoSQL 웹 서비스 사용법__ 

**[이전 문서 - What is ThanoSQL?](/)** <br>**[다음 문서 - 안내 가이드](/quick_start/algorithm_list/)**

## 시작 전 사전 정보

- 읽는데 걸리는 시간 : 3분
- 마지막 수정날짜 : 2022-06-01

## __1. ThanoSQL Web 에 접속하기__

- [ThanoSQL](https://www.thanosql.ai/)에 접속하셔서 Login 버튼을 클릭하시면 로그인 화면으로의 진입이 가능합니다.

![image](/img/how_to_use_ThanoSQL/img0.png)

!!! note ""
      ThanoSQL은 6월 1일부터 8월 31일 프로모션 기한 동안 제한 없이 모든 사람들이 무료로 이용할 수 있습니다.
      ThanoSQL 사용자들은 콘솔을 사용 하기 위해서는 필수로 회원가입을 진행해야 합니다.


## __2. 회원가입하기__

- 회원이 아닌 사용자께서는 ThanoSQL 로그인 화면에서 "회원가입 하기"를 클릭해 회원가입을 진행합니다.

![image](/img/how_to_use_ThanoSQL/img1.png)

- 회원가입 진행을 위해 이메일 인증과 비밀번호 입력을 완료 후, 동의 약관 항목에 체크 후 계정 만든다면 회원가입이 완료됩니다. 

![image](/img/how_to_use_ThanoSQL/img2.png)  

## __3. ThanoSQL Console 사용__

- 회원가입을 완료한 후 로그인 페이지로 돌아가 이메일과 비밀번호를 입력해 로그인합니다.
처음 로그인을 진행한 경우, 워크 스페이스를 생성하면 서비스를 사용할 수 있습니다.

!!! note ""
      프로모션 기간 동안에는 계정 당 한개의 워크 스페이스로 제한됩니다.

![image](/img/how_to_use_ThanoSQL/img3.png)  

!!! warning "워크 스페이스 생성시 주의사항" 
      워크 스페이스 생성시 아이디를 20자 이하의 영어 소문자와 숫자를 조합하여 사용할 수 있으며, 타인의 워크스페이스와 이름이 중복될 경우에는 생성되지 않습니다

## __4. 워크 스페이스 생성__

- 워크 스페이스를 생성하시고 나면 아래와 같이 입력하신 아이디와 함께 워크 스페이스를 확인하실 수 있습니다. 

![image](/img/how_to_use_ThanoSQL/img7.png)  

- 생성 후, 워크 스페이스 페이지 내에서 열기 버튼을 통해 ThanoSQL Console 내부로 들어갈 수 있습니다.

![image](/img/how_to_use_ThanoSQL/img4.png)

## __5. ThanoSQL 워크 스페이스 사용__
- ThanoSQL 서비스를 이용하기 위해서는 API 토큰을 사용해야 합니다. API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토큰은 더 이상 사용 할수 없는 점 유의하시기 바랍니다. 

- 자신의 워크 스페이스에서 오른쪽 상단에 보이는 GET API_TOKEN 버튼을 누르면 자동으로 API 토큰이 생성되며 클립보드에 복사 됩니다. 아래의 쿼리를 통해 정상적으로 서비스를 사용할 수 있습니다. 

```sql
%load_ext thanosql
%thanosql API_TOKEN={발급받은_API_TOKEN}
ex) %thanosql API_TOKEN=eyAQjasfn1bizd2badf

```

![image](/img/how_to_use_ThanoSQL/img6.png)

!!! danger  
      ThanoSQL 워크 스페이스 상에서 ThanoSQL 문법을 사용하기 위해서는 파일 상단에서 항상 위 쿼리를 실행시켜야 합니다. 
