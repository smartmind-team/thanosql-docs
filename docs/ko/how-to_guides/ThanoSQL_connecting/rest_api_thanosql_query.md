---
title: Query APIs
---

# **Query APIs**

ThanoSQL 쿼리를 실행하고 쿼리 로그를 응답으로 받습니다.

!!! Note "__Query Log__"
    쿼리 로그는 실행된 쿼리에 대한 정보가 포함됩니다. 아래는 쿼리 로그를 구성하는 속성입니다.

    - `query_id`: 쿼리를 식별하는 고유한 ID입니다.
    - `statement_type`: 쿼리 유형 (예: SELECT, BUILD 등).
    - `start_time`: 쿼리 시작 시간입니다.
    - `end_time`: 쿼리 종료 시간입니다.
    - `query`: 실행된 쿼리문입니다.
    - `referer`: REST API 호출이 발생한 위치를 나타냅니다.
    - `state`: 쿼리 실행 상태를 나타냅니다 (RUNNING 또는 COMPLETE).
    - `destination_table`: 결과가 저장된 테이블을 나타냅니다 (`statement_type`이 SELECT인 경우 새로운 `destination_table`이 생성됩니다. 그렇지 않은 경우, `destination_table`은 영향을 받은 테이블을 나타냅니다).
    - `error_result`: 쿼리 실행 중 발생한 오류 메시지를 저장합니다.
    - `created_at`: 쿼리 로그가 생성된 시간을 나타냅니다.

!!! warning "__Warning__"
    - "__CONVERT__"를 사용해 만들어진 컬럼의 값들은 base64로 인코딩됩니다. 바이트 형식의 값을 사용하려면 base64의 b64decode를 사용하여 디코딩해야 합니다.

## __`POST` /query__

!!! Note "__Parameters__"
    
    - `schema`: 테이블을 가져올 스키마입니다. 매개변수가 제공되지 않은 경우 "qm"으로 기본 설정됩니다.
    - `table_name`: 테이블을 생성하는 데 사용할 이름입니다. 매개변수가 제공되지 않은 경우 무작위로 테이블 이름을 생성합니다.
    - `overwrite`: 테이블을 덮어쓸지 여부를 결정합니다. 기본값은 False입니다.

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

!!! faq "FAQ" 
    - Jupyter 내부의 path를 조회하기 위해서는 앞에 /home/jovyan 을 붙여야 합니다.