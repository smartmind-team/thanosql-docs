---
title: Search APIs
---

# **Search APIs**

Search APIs로 이미지나 텍스트와 "__BUILD__"한 모델을 기반으로 ThanoSQL 워크스페이스 데이터베이스 상의 유사한 이미지를 조회하고 받을 수 있습니다. 

## __`POST` /search/file__

이미지 파일을 사용하여 이미지를 검색하려면 다음 엔드포인트를 사용할 수 있습니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/search/file/"
    table_name = "테이블 명"
    model_name = "모델 명"
    column_name = "컬럼 명"

    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    files = {'file': open('이미지 파일 경로', 'rb')}

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, files=files, stream=True) as r:
        r.raise_for_status()
        with open("저장할 zip 파일 경로", 'wb') as f:
            for chunk in r.iter_content(chunk_size=8192):
                f.write(chunk)
    ```

=== "cURL"

    ```shell 
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/search/file/?table_name={table_name}&model_name={model_name}&column_name={column_name}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@이미지 파일 경로;type=image/이미지 파일 타입'
    ```

## __`POST` /search/text__

텍스트를 사용하여 이미지를 검색하려면 다음 엔드포인트를 사용할 수 있습니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/search/text/"
    table_name = "테이블 명"
    model_name = "모델 명"
    column_name = "컬럼 명"
    text = '서치할 텍스트'


    ## WHEN SEARCHING WITH IMAGE
    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}&text={text}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, stream=True) as r:
        r.raise_for_status()
        with open("저장할 zip 파일 경로", 'wb') as f:
            for chunk in r.iter_content(chunk_size=8192):
                f.write(chunk)
    ```

=== "cURL"

    ```shell 
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/search/text/?table_name={table_name}&model_name={model_name}&column_name={column_name}&text={text}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -d ''
    ```

!!! faq "FAQ" 
    - ThanoSQL 서치는 한 번의 API Call 당 하나의 이미지나 텍스트를 서치할 수 있습니다.
    - Jupyter 내부의 path를 조회하기 위해서는 앞에 /home/jovyan 을 붙여야 합니다.