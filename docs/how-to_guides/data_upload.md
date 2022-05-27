# __데이터 불러오기__ 

## __개요__

데이터를 불러오는 두가지 방법이 있습니다. 첫 번째 방법으로는 워크스페이스 환경에 직접 데이터를 드래그 앤 드랍으로 업로드하는 방법이고, 두 번째 방법은 개인키를 사용하여 SFTP 연결하여 로컬 데이터를 불러오는 방법입니다. 

<br>

## __(1) 워크스페이스 환경에 데이터 불러오기__
ThanoSQL 워크스페이스의 기본 사용법은 jupyter lab과 동일합니다.
upload -> 파일선택 




## __(2) 원격 시스템에 접속하여 파일 불러오기(SFTP) - ubuntu/linux__
sftp 명령은 ftp와 유사한 사용자 인터페이스가 있는 대화식 파일 전송 프로그램입니다. 하지만 sftp는 SSH FTP(File Transfer Protocol)를 사용하여 서버에 대한 보안 연결을 만듭니다. ftp 명령으로 사용할 수 있는 옵션 중 일부는 sftp 명령에 포함되어 있지 않지만 대부분의 명령이 포함되어 있습니다.

<br>

### __1. 개인키 / ID 확인하기__
SFTP 연결을 하기 위해서는 개인키와 ID가 필요합니다. 웹페이지 상단 '내 프로파일'에 들어가면 개인키를 확인 또는 재발급 받으실 수 있습니다. SFTP 접속에 필요한 ID는 회사 ID를 입력하세요.

개인키는 다음과 같은 형태로 구성되어 있습니다.
```pem
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaCtdjEAAAAABG5vbmUtesttesttestteZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAEAwf0AHzaPEz3m81l56ScriVThc4v/9/erUZQtaRkT6TRasdasdrY96O4AZE
3bprMXloa1d73wESoGasdsaneoznug3XEufi2r3Iom7QKS1yF90sf3SSsaU8LGadMLQ5fd
VEHDk2K94Y6120IH/SUs8mKnh1DiasdoplgbLrWv90TYLl7WAInyruads0hYQTedZhdWYz
WLZlSO0bac+8N8SPHLFr2VgKUmzZatpdasJwIk6nAP23aYuYD5GIH/cEQvvquhSNsaHKhm
AdnUS6MlSQxD2LOnBEPNdadzzseJs9fPJtR0Zgdw9sFzm24c/0ixxs7RUd+56hbyviadow
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
EAAMH9AB82jxM95vNZeeknK4lU4XOdas/dE2C5e1gCJ8q7tIWEE3mYXVmM1i2ZUjtAEdhB
KG3Ejxyxa9lasdaYClJadasybUdGYHcPbBc5tuHP9IscbO0VHfueoW8r4qMIgBUBYip1BR
7lEc2xXpOA23asdasdgzxMzUGvpuzvBJg0oeW3J6NiDtOVdyKnUpjo6Off5QTeoRRQ1+Sd
0HKZ32q7f0yozK9gRublbeilsTzEkWhvnDz7aobfsTiN2JXvGp3rx7manR4adsasds/JrM
u9O1QqfvqUTcd++ax1WGk5CPvsCYLninZXXqR7y8IQ5HWRIFEBsI4rLfoJmmKZgepTa2zw
d0mmCMjP5HzeikNoLJhyunlOTUUtoIzwAAAMEAyUD2N93Iwf09rlNYrOURANjXJ7JLnVOT
iqgFRgzG7ELjBlVknasdasdVyQSwXqPQeV0LKqIK/pro3u84c20U9G3Hj6/RU/spVgMFLu
yQmkvxp//a7emwLtUgOa76IYckBePZUduaELPWdjVOliajZyOEIlUPslD7cTr1zLffhNBx
l2J5BknmWy4lng3m44vE2f7ZmDCdmopK6EXQs3vPxbJkaROUQTEqQAsBFLU1KkkgAJDkGc
kp1GuW0kIgRvSHAAAAE3Jvb3RAdGVzdGluZy1zZXJ2ZXIBAgMEBQYHasdasdasdasdsdww
-----END OPENSSH PRIVATE KEY-----
```

<br>

### __3. 로컬에 개인키 등록하기 - pem 파일 생성__
로컬 환경에 vim을 사용하여 'key.pem'파일을 생성해 줍니다. 생성된 pem 파일에 __2.__ 에서 발급받은 개인키를 붙여넣습니다. 

```bash
$ vim key.pem
```

<br>

### __4. pem 파일 권한 변경__
개인키가 등록된 pem 파일의 권한을 read로 변경해줍니다.

```bash
$ chmod 400 key.pem
```

<br>

### __5. SFTP 연결하기__
파일 권한이 변경 완료되면 SFTP에 접속해 줍니다.
SFTP에 접속하기 위해서는 pem 파일의 절대 경로와 __2.__ 에서 확인한 ID가 필요합니다.

```bash
sftp -i [pem파일 절대경로] [ID]@engine.thanosql.ai
```

연결되면 아래와 같은 화면을 확인하실 수 있습니다.

![IMAGE](img\data_upload_img1.png)

<br>

### __5. 데이터 파일 접속하기__
SFTP 접속이 완료되었다면 현재 위치를 파악하고 권한이 부여된 'drive' 폴더에 접속합니다. ('drive' 폴더 외에는 권한이 부여되지 않아 접속이 불가합니다.)

```bash
    sftp> pwd
    sftp> cd /drive
```

<br>

### __6. (옵션)내 폴더 생성하기 --- 필요한가?__
'drive' 폴더 안에 나만의 폴더를 생성하는 방법입니다.

```bash
    mkdir my_folder
```

아래 코드를 사용하여 만들어진 폴더에 접속합니다.

```bash
    cd my_folder
```

<br>

### __7. 폴더/파일 전송하기__
원격 작업 디렉토리와 로컬 작업 디렉토리를 확인하고 파일을 전송합니다.

```bash
    put image_folder
```

<br>

### __필수 SFTP 명령 테이블__
|명령|설명|
|---|---|
|sftp remote-system|원격 시스템에 대한 sftp 연결을 설정합니다. 자세한 내용은 원격 시스템에 대한 sftp 연결을 열고 닫는 방법을 참조하십시오.|
|sftp remote-system: file|remote-system에서 명명된 file을 복사합니다.|
|bye|sftp 세션을 종료합니다.|
|help|sftp 명령을 모두 나열합니다.|
|ls|원격 작업 디렉토리의 내용을 나열합니다.|
|lls|로컬 작업 디렉토리의 내용을 나열합니다.|
|pwd|원격 작업 디렉토리의 이름을 표시합니다.|
|cd|원격 작업 디렉토리를 변경합니다.|
|lcd|로컬 작업 디렉토리를 변경합니다.|
|mkdir|원격 시스템에서 디렉토리를 만듭니다.|
|rmdir|원격 시스템에서 디렉토리를 삭제합니다.|
|get|원격 작업 디렉토리에서 로컬 작업 디렉토리로 파일을 복사합니다.|
|put|로컬 작업 디렉토리에서 원격 작업 디렉토리로 파일을 복사합니다.|
|delete|원격 작업 디렉토리에서 파일을 삭제합니다.|

<br>