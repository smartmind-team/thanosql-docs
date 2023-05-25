---
title: File APIs
---

# **File APIs**

File APIs로 자신의 워크스페이스 저장 공간에 원격으로 파일을 보내고 원하는 데이터베이스 테이블에 파일을 넣을수 있습니다. 사용자 파일을 저장하는 기본 폴더는 "drive" 입니다.

!!! warning "__Warning__"
    - File API는 아래의 확장자의 이미지, 오디오, 그리고 비디오 데이터를 지원합니다:
        - Image:  "jpg", "png", "jpeg"
        - Audio: "mp3", "wav
        - Video: "mp4", "wmv", "avi"

    - 위에서 언급되지 않은 모든 확장자의 파일은 "drive/others" 폴더에 저장됩니다. 

## __`POST` /file/upload__

파일만 업로드하려면 아래의 방법을 사용하여 파일을 워크스페이스 저장소로 보냅니다.
API URL에 "dir=폴더 이름"을 덧붙이면 파일은 지정한 폴더에 저장됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/file/upload/"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('데이터 파일 경로', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/upload/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@데이터 파일 경로;type=파일_타입/데이터 파일 타입'
    ```

"db commit"이 True로 설정되고 "table name"과 "column name"이 지정되면 지정된 파일이 ThanoSQL 저장소로 전송되고 파일 경로가 지정된 테이블의 열에 삽입됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/upload/"
    table_name = "테이블 명"
    column_name = "컬럼 명"
    db_commit = True 

    api_url = f"{base_url}?db_commit={db_commit}&table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('데이터 파일 경로', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell 
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/upload/?db_commit=True&table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@데이터 파일 경로;type=파일_타입/데이터 파일 타입'
    ```

!!! faq "__FAQ__"
    - Jupyter 내부의 파일을 사용하려면 경로 앞에 '/'home/jovyan'을 붙여야 합니다.


## __`POST` /file/delete__

파일만 삭제하려면 아래의 방법을 사용하여 파일을 워크스페이스 저장소에서 삭제합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    file_path = "데이터 파일 경로"
    api_url = f"https://{your-engine-url}/api/v1/file/delete/?file_path={file_path}'

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/delete/?file_path=데이터 파일 경로' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' 
    ```

"db_commit"이 True로 설정되고 "table_name" 및 "column_name"이 지정되면 지정된 파일을 워크스페이스 저장소에서 삭제하고 그 파일 경로와 같은 값이 지정된 열에 들어 있는 모든 행이 지정된 데이터베이스에서 제거됩니다.


=== "Python"

    ```python 
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/file/delete/"
    db_commit = True 
    file_path = '데이터 파일 경로',
    table_name = '테이블 명',
    column_name = '컬럼 명'

    api_url = f"{base_url}?db_commit={db_commit}&file_path={file_path}&table_name={table_name}&column_name={column_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/delete/?db_commit=True&file_path=데이터 파일 경로&table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```

## __`POST` /file/list__

지정한 경로의 파일과 폴더 목록을 가져옵니다. 파일 경로를 지정할 때 정규식을 사용할 수 있습니다.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    file_path = "파일 경로"
    api_url = f"https://{your-engine-url}/api/v1/file/list/?file_path={file_path}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/list/?file_path={파일 경로}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```
