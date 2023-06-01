---
title: Query APIs
---

# **Query APIs**

## __`POST` /query__

ThanoSQL 쿼리를 실행하고 쿼리 로그를 응답으로 받습니다.

=== "Python"

    ```python
    import requests

    api_token = "발급받은_API_TOKEN"
    api_url="https://{your-engine-url}/api/v1/query/"
    query="요청할 쿼리"
    query_type="쿼리를 실행할 sql type" #psql or thanosql

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'query_string': query, 'query_type': query_type
    }

    r = requests.post(api_url, json=data, headers=header)

    r.raise_for_status()
    return_json = r.json()
    
    ```

=== "cURL"

    ```shell 
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/query/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_string": query, "query_type": query_type}'
    ```

### __Parameters__
    
파라미터로 쿼리 결과를 저장할 ‘대상 테이블‘을 설정할 수 있습니다. 아무것도 설정하지 않으면 qm 스키마의 임시테이블이 ‘대상 테이블‘이 됩니다.

- `schema`: 테이블을 가져올 스키마입니다. 매개변수가 제공되지 않은 경우 "qm"으로 기본 설정됩니다.
- `table_name`: 테이블을 생성하는 데 사용할 이름입니다. 매개변수가 제공되지 않은 경우 무작위로 테이블 이름을 생성합니다.
- `overwrite`: 테이블을 덮어쓸지 여부를 결정합니다. 기본값은 False입니다.

### __Response__
    
응답 형태는 아래의 ‘쿼리 로그’ 형식입니다. 

`statement_type이` SELECT, LIST 등과 같이 결과가 있으면 파라미터로 설정한 ‘대상 테이블‘로 결과를 저장합니다. 이 ‘대상 테이블’ 정보는 `destination_schema`, `detination_table_name` 값으로 확인 가능합니다. (쿼리 결과가 없을 경우 파라미터 값으로 따로 지정된 테이블이 아닌 쿼리로 영향을 받은 테이블이 ‘대상 테이블’이 됩니다.)


!!! Note "__Query Log__"
    쿼리 로그는 실행된 쿼리에 대한 정보가 포함됩니다. 아래는 쿼리 로그를 구성하는 속성입니다.

    - `query_id`: 쿼리를 식별하는 고유한 ID입니다.
    - `statement_type`: 쿼리 유형 (예: SELECT, BUILD 등).
    - `start_time`: 쿼리 시작 시간입니다.
    - `end_time`: 쿼리 종료 시간입니다.
    - `query`: 실행된 쿼리문입니다.
    - `referer`: REST API 호출이 발생한 위치를 나타냅니다.
    - `state`: 쿼리 실행 상태를 나타냅니다 (RUNNING 또는 COMPLETE).
    - `destination_table_name`: 쿼리로 영향을 받은 테이블의 이름을 나타냅니다.
    - `destination_schema`: 대상 테이블의 스키마를 나타냅니다.
    - `error_result`: 쿼리 실행 중 발생한 오류 메시지를 저장합니다.
    - `created_at`: 쿼리 로그가 생성된 시간을 나타냅니다.

!!! warning "__Warning__"
    - "__CONVERT__"를 사용해 만들어진 컬럼의 값들은 base64로 인코딩됩니다. 바이트 형식의 값을 사용하려면 base64의 b64decode를 사용하여 디코딩해야 합니다.

