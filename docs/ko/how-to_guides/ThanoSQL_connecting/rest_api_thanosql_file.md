---
title: ThanoSQL 워크스페이스 데이터베이스 파일 업로드 & 삭제
---

# __ThanoSQL 워크스페이스 데이터베이스 파일 업로드__

REST API를 사용하여 자신의 ThanoSQL 저장 공간에 원격으로 파일을 보내고 원하는 데이터베이스 테이블에 파일을 넣을수 있습니다.

!!! warning "__Warning__"
    - File API는 아래의 확장자의 이미지, 오디오, 그리고 비디오 데이터를 지원합니다:
        - Image:  "jpg", "png", "jpeg"
        - Audio: "mp3", "wav
        - Video: "mp4", "wmv", "avi"

## 파일 업로드

파일만 업로드하려면 아래의 방법을 사용하여 파일을 ThanoSQL 저장소로 보냅니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/file/upload/"
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
      'https://engine.thanosql.ai/api/v1/file/upload/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@데이터 파일 경로;type=파일_타입/데이터 파일 타입'
    ```

## 파일 업로드 & 인서트

"db commit"이 True로 설정되고 "table name"과 "column name"이 지정되면 지정된 파일이 ThanoSQL 저장소로 전송되고 파일 경로가 지정된 테이블의 열에 삽입됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/upload/"
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
      'https://engine.thanosql.ai/api/v1/file/upload/?db_commit=True&table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@데이터 파일 경로;type=파일_타입/데이터 파일 타입'
    ```

!!! faq "FAQ"
    - Jupyter 내부의 파일을 사용하려면 경로 앞에 '/'home/jovyan'을 붙여야 합니다.



# __ThanoSQL 워크스페이스 데이터베이스 파일 삭제__

REST API를 사용하여 자신의 ThanoSQL 저장 공간에 원격으로 파일을 삭제하고 데이터베이스 테이블에 파일 경로가 담긴 행이 있다면 함께 삭제할 수 있습니다.

## 파일 삭제

파일만 삭제하려면 아래의 방법을 사용하여 파일을 ThanoSQL 저장소에서 삭제합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/file/delete/"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'file_path': '데이터 파일 경로'
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/file/delete/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"file_path": "데이터 파일 경로"}'
    ```

## File Delete & Remove 

"db_commit"이 True로 설정되고 "table_name" 및 "tablen_name"이 지정되면 지정된 파일을 ThanoSQL 저장소에서 삭제하고 지정된 파일 경로와 동일한 값을 가진 모든 행이 테이블의 지정된 열에서 제거됩니다.


== "Python"

    ```python 
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/file/delete/"
    db_commit = True 

    api_url = f"{base_url}?db_commit={db_commit}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'file_path': '데이터 파일 경로',
        'table_name': '테이블 명',
        'column_name': '컬럼 명'
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/file/delete/?db_commit=True' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"file_path": "데이터 파일 경로", "table_name": "테이블 명", "column_name": "컬럼 명"}'
    ```