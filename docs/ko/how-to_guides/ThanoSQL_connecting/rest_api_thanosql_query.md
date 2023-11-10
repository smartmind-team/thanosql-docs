---
title: Query APIs
---

# **Query APIs**

## __`POST` /query__

ThanoSQL 쿼리를 실행하고 쿼리 로그 결과를 응답으로 받습니다. 쿼리는 직접 입력하거나 기존 쿼리 템플릿을 사용하여 불러 올 수 있습니다(매개변수 포함 또는 제외).

### 템플릿 사용하지 않는 쿼리

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/query/"
    query = "요청할 쿼리"
    query_type = "쿼리를 실행할 SQL 타입" # psql 또는 thanosql

    header = {
        "Authorization": f"Bearer {api_token}"
    }    

    data = {
        'query_string': query, 'query_type': query_type
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)
    r.raise_for_status()
    r.json()
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

### 템플릿 사용하는 쿼리

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/query/"
    template_name = "사용할 템플릿 이름"  # template_id로 쿼리하는 경우에는 이 줄이 필요하지 않습니다
    template_id = "사용할 템플릿의 ID"  # template_name로 쿼리하는 경우에는 이 줄이 필요하지 않습니다
    parameters = "템플릿을 채울 매개변수 매핑"  # 딕셔너리
    query_type = "쿼리를 실행할 SQL 타입"  # psql 또는 thanosql

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'query_type': query_type,
        'template_name': template_name,  # 또는 template_id로 쿼리하는 경우 `'template_id': template_id`
        'parameters': parameters         # 선택 사항, 템플릿 필요한지 여부에 따라
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/query/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_type": query_type, "template_name": template_name, "parameters": parameters}'
    ```

!!! warning "쿼리 문자열 및 쿼리 템플릿"
    쿼리 문자열과 템플릿(및/또는 매개변수)은 함께 사용해서는 안 됩니다. `template_id`, `template_name`, `parameters` 중 하나 이상과 함께 `query_string`을 사용하면 에러가 발생합니다.

### __Parameters__
    
파라미터로 쿼리 결과를 저장할 ‘대상 테이블‘을 설정할 수 있습니다. 아무것도 설정하지 않으면 qm 스키마의 임시테이블이 ‘대상 테이블‘이 됩니다.

- `schema`: 테이블을 가져올 스키마입니다. 매개변수가 제공되지 않은 경우 "qm"으로 기본 설정됩니다.
- `table_name`: 테이블을 생성하는 데 사용할 이름입니다. 매개변수가 제공되지 않은 경우 무작위로 테이블 이름을 생성합니다.
- `overwrite`: 테이블을 덮어쓸지 여부를 결정합니다. 기본값은 False입니다.
- `max_results`: 결과가 있는 쿼리 일 경우 반환되는 결과의 최대 수를 지정합니다. 기본값은 0 최대값은 100 입니다. 

### __Response__

응답으로 쿼리 로그와 레코드를 반환합니다.

```json
{
    ...QueryLog,
    "records" : [{}] | null # 쿼리 결과
}
```

!!! Note "__Query Log__"
    쿼리 로그 결과는 실행된 쿼리에 대한 정보가 포함됩니다. 아래는 쿼리 로그 결과를 구성하는 속성입니다.

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

`statement_type이` SELECT, LIST 등과 같이 결과가 있으면 파라미터로 설정한 ‘대상 테이블‘로 결과를 저장합니다. 이 ‘대상 테이블’ 정보는 `destination_schema`, `detination_table_name` 값으로 확인 가능합니다. (쿼리 결과가 없을 경우 파라미터 값으로 따로 지정된 테이블이 아닌 쿼리로 영향을 받은 테이블이 ‘대상 테이블’이 됩니다.)

!!! warning "__Warning__"
    - "__CONVERT__"를 사용해 만들어진 컬럼의 값들은 base64로 인코딩됩니다. 바이트 형식의 값을 사용하려면 base64의 b64decode를 사용하여 디코딩해야 합니다.


## **`GET` /query/log**

모든 쿼리 로그의 paginated 목록을 검색합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/log"
    search = "검색 키워드"
    offset = "오프셋"
    limit = "리밋"

    api_url = f"{base_url}?search={search}&offset={offset}&limit={limit}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.get(api_url, headers=header):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'GET' \
      'https://{your-engine-url}/api/v1/query/log/?search={search}&offset={offset}&limit={limit}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```

### __Parameters__

- `search`: 반환된 로그의 쿼리 구문에 포함되어야 하는 텍스트입니다 (기본: 빈 문자열).
- `offset`: 쿼리 로그 조회를 시작할 기준점입니다 (기본값: 0).
- `limit`: offset에서 시작하여 조회 할 결과의 개수입니다 (기본: 100, 최대: 100).


## **`GET` /query/template**

모든 쿼리 템플릿을 불러옵니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    offset = {오프셋}
    limit = {리밋}
    search = "검색 키워드"
    order_by = "정렬 기준"

    api_url = f"{base_url}?offset={offset}&limit={limit}&search={search}&order_by={order_by}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.get(api_url, headers=header):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'GET' \
      'https://{your-engine-url}/api/v1/query/template/?offset={offset}&limit={limit}&search={search}&order_by={order_by}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```

### __Parameters__

- `offset`: 쿼리 템플렛 조회를 시작할 기준점입니다 (기본값: 0).
- `limit`: offset에서 시작하여 조회 할 결과의 개수입니다 (기본: 100, 최대: 100).
- `search`: 반환된 템플렛의 이름에 포함되어야 하는 텍스트입니다 (기본: 빈 문자열).
- `order_by`: 쿼리 템플렛 결과의 순서를 지정하는 방법입니다. 세 가지 가능한 값이 있습니다: 최근에서 가장 오래된 순으로 정렬 하는 'recent', A에서 Z까지 이름을 기준으로 하는 'name_asc', Z에서 A까지 이름을 기준으로 하는 'name_desc'입니다 (기본: 'recent').

### __Response__

이 메서드는 쿼리 템플릿 목록(아래 표시)을 응답으로 표시합니다.

!!! Note "__Query Template__"
    쿼리 템플릿은 매개변수가 필요/필요하지 않을 수도 있는 쿼리 문자열입니다. psql과 thanosql 모두 템플릿은 [Jinja](https://jinja.palletsprojects.com/en/3.1.x/)에서 사용하는 규칙을 따릅니다. 다음은 쿼리 템플릿을 구성하는 속성입니다.

    - `id`: 쿼리 템플릿을 식별하는 고유 ID 번호입니다.
    - `name`: 쿼리 템플릿 이름입니다.
    - `query`: 쿼리 템플릿 문자열입니다.
    - `created_at`: 쿼리 템플릿이 생성된 시점의 타임스탬프를 표시합니다.
    - `updated_at`: 쿼리 템플릿이 마지막으로 수정된 타임스탬프를 표시합니다.

    매개변수가 필요한 템플릿의 예:

    ```sql
    INSERT INTO {{ schema }}.\"{{ name }}\" ({% for column, _ in data.items() -%}{{ column }}{% if not loop.last %}, {% endif %}{%- endfor %})
    VALUES ({% for _, value in data.items() -%}{% if value is string %}'{{ value }}'{% else %}{{ value }}{% endif %}{% if not loop.last %}, {% endif %}{%- endfor %});
    ```
    위의 템플릿은 매개변수 `schema`, `name` 및 `data`가 필요합니다. 반면에 아래 템플릿은 이미 완성되었으므로 유효한 쿼리가 되기 위해 매개변수가 필요하지 않습니다:

    ```sql
    GET THANOSQL DATASET diet_data
    OPTIONS (overwrite=True)
    ```


## **`POST` /query/template**

이름과 쿼리 문자열의 새 쿼리 템플릿을 생성합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/query/template"
    name = "쿼리 템플릿의 이름"
    query = "쿼리 템플릿 문자열 (psql로 또는 thanosql로)"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'name': name, 'query': query
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/query/template' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"name": name, "query": query}'
    ```


## **`GET` /query/template/{template_name}**

지정된 이름의 쿼리 템플릿을 검색합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    template_name = "쿼리 템플릿의 이름"

    api_url = f"{base_url}/{template_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.get(api_url, headers=header)
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'GET' \
      'https://{your-engine-url}/api/v1/query/template/{template_name}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```


## **`PUT` /query/template/{template_name}**

특정 이름의 쿼리 템플릿을 업데이트합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    template_name = "쿼리 템플릿의 이름"
    name = "쿼리 템플릿의 새 이름"
    query = "쿼리 템플릿 새 문자열 (psql로 또는 thanosql로)"

    api_url = f"{base_url}/{template_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'name': name, 'query': query
    }

    r = requests.put(api_url, data=json.dumps(data), headers=header)
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'PUT' \
      'https://{your-engine-url}/api/v1/query/template/{template_name}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"name": name, "query": query}'
    ```

!!! Note
    하나의 필드만 업데이트하려는 경우 다른 필드는 JSON에서 제외하십시오. 예를 들어 쿼리 템플릿의 이름만 업데이트하려는 경우 데이터 본문에 `{"query": query}`만 사용합니다.


## **`DELETE` /query/template/{template_name}**

특정 이름의 쿼리 템플릿을 삭제합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    template_name = "쿼리 템플릿의 이름"

    api_url = f"{base_url}/{template_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.delete(api_url, headers=header)
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'DELETE' \
      'https://{your-engine-url}/api/v1/query/template/{template_name}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```
