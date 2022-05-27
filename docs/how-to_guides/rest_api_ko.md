
# REST API를 이용한 ThanoSQL 사용 가이드 

## 인증 가능한 토큰 만들기
ThanoSQL의 REST API를 사용하기 위해서는 자신만의 토큰을 발행해야 합니다. 토큰을 발행하기 위해서는 https://thanosql.ai를 방문하여 밑의 절차에 따라 간단하게 받아보실수 있습니다. API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토크은 더 이상 사용 할수 없는 점 유의 하시기 바랍니다. 


screen shots </br>
screen shots </br>
screen shots </br>
screen shots </br>

## ThanoSQL 쿼리 사용하기 
REST API를 사용하여 ThanoSQL 콘솔상에서 사용하던 쿼리들을 사용할수 있습니다. 

=== "Python"

    ``` python
    import requests
    import json

    api_token = "발급받은 API TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/query"
    query="요청할 쿼리"

    header = {
        "Authorization" : f"Bearer {api_token}"
    }

    data = {
        'query_string' : query
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()
    
    ```

=== "cURL"

    ``` shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/query/?table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은 API TOKEN' \
      -d '{"query_string": query}'
    ```

## ThanoSQL 인서트 사용하기 
REST API를 사용하여 자신의 ThanoSQL 저장공간에 원격으로 이미지를 보내고 원하는 데이터베이스 테이블에 이미지를 넣을수 있습니다. 


=== "Python"

    ``` python
    import requests
    import json

    api_token = "발급받은 API TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/insert"
    table_name = "테이블 명"
    column_name = "컬럼 명"

    api_url = f"{base_url}?table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization" : f"Bearer {api_token}"
    }
    files = {'file' : open('이미지 파일 경로', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ``` shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/insert/?table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은 API TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@이미지 파일 경로;type=image/이미지 파일 타입'
    ```

## ThanosQL 서치 사용하기 
REST API를 사용하여 이미지나 텍스트와 BUILD한 모델을 기반으로 ThanoSQL 데이터베이스 상의 유사한 이미지를 조회하고 받을 수 있습니다. 

### 이미지로 이미지 서치 하기 

=== "Python"

    ``` python
    import requests
    import json

    api_token = "발급받은 API TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/search/file"
    table_name = "테이블 명"
    model_name = "모델 명"
    column_name = "컬럼 명"

    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}"

    header = {
        "Authorization" : f"Bearer {api_token}"
    }

    files = {'file' : open('이미지 파일 경로', 'rb')}

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, files=files, stream=True) as r:
        r.raise_for_status()
        with open("저장할 zip 파일 경로", 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
    ```

=== "cURL"

    ``` shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/search/file/?table_name=테이블 명&model_name=모델 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은 API TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@이미지 파일 경로;type=image/이미지 파일 타입'
    ```

### 텍스트로 이미지 서치 하기 

=== "Python"

    ``` python
    import requests
    import json

    api_token = "발급받은 API TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/search/text"
    table_name = "테이블 명"
    model_name = "모델 명"
    column_name = "컬럼 명"
    text = '서치할 텍스트'


    ## WHEN SEARCHING WITH IMAGE
    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}&text={text}"

    header = {
        "Authorization" : f"Bearer {api_token}"
    }

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, stream=True) as r:
        r.raise_for_status()
        with open("저장할 zip 파일 경로", 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
    ```

=== "cURL"

    ``` shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/search/text/?table_name=테이블 명&model_name=모델 명&column_name=컬럼 명&text=서치할 텍스트' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은 API TOKEN' \
      -d ''
    ```
