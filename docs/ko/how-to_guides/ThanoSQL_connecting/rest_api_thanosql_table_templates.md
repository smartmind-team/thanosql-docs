---
title: Table Template APIs
---

# **Table Template APIs**
Table Template APIs로 ThanoSQL 워크스페이스 데이터베이스 테이블 템플렛에 여러 CRUD 작업을 수행할 수 있습니다.

!!! Note "__Table Template Object__"
    테이블 템플릿 개체는 다섯 가지 주요 요소로 구성됩니다:

    1. `name`: 테이블 템플릿의 이름
    2. `table_template`: 테이블 템플릿의 body, 칼럼들과 제약조건들로 구성됩니다(자세한 내용은 [테이블 객체 문서](../rest_api_thanosql_tables) 참조하세요)
    3. `version`: 테이블 템플릿의 버전(현재 이 값은 변경 불가)
    4. `compatibility`: 테이블 템플릿의 호환성(`backward`, `forward`, `full`, 또는 `none` 중 하나)
    5. `created_at`: 테이블 템플릿의 생성한 날짜


## **`GET` /table_template**

모든 테이블 템플릿 목록을 특정 순서로 검색합니다. 선택적으로 검색 키워드 사용할 수도 있습니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/table_template/"
    search = "검색 키워드"
    order_by = "정렬 기준"
    latest = {최신: True or False}

    api_url = f"{base_url}?search={search}&order_by={order_by}&latest={latest}"

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
      'https://{your-engine-url}/api/v1/table_template/?search={search}&order_by={order_by}&latest={latest}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```

### __Parameters__

- `search`: 반환된 테이블 템플릿의 이름에 포함되어야 하는 단어입니다 (기본: 빈 문자열).
- `order_by`: 결과의 순서를 지정하는 방법입니다. 세 가지 가능한 값이 있습니다: 최근에서 가장 오래된 순으로 정렬 하는 'recent', A에서 Z까지 이름을 기준으로 하는 'name_asc', Z에서 A까지 이름을 기준으로 하는 'name_desc'입니다 (기본: 'recent').
- `latest`: 각 테이블 템플릿의 최신 버전만 반환하려는 경우 `True`로 설정할 수 있는 부울 값입니다. 그렇지 않으면 모든 버전을 반환합니다 (기본: `False`).


## **`GET` /table_template/{table_template_name}**

특정 이름의 테이블 템플릿을 불러오기 위해 사용됩니다. 필요한 경우 특정 버전이나 최신 버전을 요청에 추가할 수도 있습니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table_template"
    table_template_name = "테이블 템플릿 이름"
    version = "특정 버전 또는 최신 테이블 템플릿을 요청하려면 'latest'"

    api_url = f"{base_url}/{table_template_name}?version={version}"

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
      'https://{your-engine-url}/api/v1/table_template/{table_template_name}?version={version}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```

### __Response__

API의 응답 구조는 다음과 같습니다:

```
{
  "table_templates": ["요청된 이름의 모든 테이블 템플릿 목록 또는 'version'이 명시 되어 있으면 테이블 템플릿 하나"]
  "versions": ["해당 테이블 템플렛의 모든 버전 목록입니다"]
}
```


## **`POST` /table_template/{table_template_name}**

새 테이블 템플릿을 만듭니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/table_template/"
    table_template_name = "테이블 템플릿 이름"

    data = {
    "table_template": {
        "columns": [
        {
            "default": "nextval('accounts_user_id_seq'::regclass)",
            "is_nullable": False,
            "type": "integer",
            "name": "id"
        },
        {
            "default": None,
            "is_nullable": True,
            "type": "character varying",
            "name": "username"
        },
        {
            "default": None,
            "is_nullable": False,
            "type": "character varying",
            "name": "password"
        },
        {
            "default": None,
            "is_nullable": True,
            "type": "character varying",
            "name": "role_id"
        }
        ],
        "constraints": {
        "primary_key": {
            "name": "user_id_pkey",
            "columns": ["id"]
        },
        "foreign_keys": [
            {
                "name": "user_role_id_fkey",
                "reference_schema": "public",
                "reference_column": "role_id",
                "reference_table": "roles",
                "column": "role_id"
            }
        ]
        }
    },
    "version": "string",
    "compatibility": "string"
    }

    api_url = f"{base_url}/{table_template_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header, body=data):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'POST' \
    'https://{your-engine-url}/api/v1/table_template/{table_template_name}' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -d '{
        "table_template": {
            "columns": [
            {
                "default": "nextval('accounts_user_id_seq'::regclass)",
                "is_nullable": False,
                "type": "integer",
                "name": "id"
            },
            {
                "default": None,
                "is_nullable": True,
                "type": "character varying",
                "name": "username"
            },
            {
                "default": None,
                "is_nullable": False,
                "type": "character varying",
                "name": "password"
            },
            {
                "default": None,
                "is_nullable": True,
                "type": "character varying",
                "name": "role_id"
            }
            ],
            "constraints": {
            "primary_key": {
                "name": "user_id_pkey",
                "columns": ["id"]
            },
            "foreign_keys": [
                {
                    "name": "user_role_id_fkey",
                    "reference_schema": "public",
                    "reference_column": "role_id",
                    "reference_table": "roles",
                    "column": "role_id"
                }
            ]
            }
        },
        "version": "string",
        "compatibility": "string"
        }'
    ```


## **`DELETE` /table_template/{table_template_name}**

특정 이름의 테이블 템플릿을 삭제합니다. 필요한 경우 특정 버전만 삭제할 수도 있습니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table_template"
    table_template_name = "테이블 템플릿 이름"
    version = "테이블 템플릿의 특정 버전"

    api_url = f"{base_url}/{table_template_name}?version={version}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.delete(api_url, headers=header):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'DELETE' \
      'https://{your-engine-url}/api/v1/table_template/{table_template_name}?version={version}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN'
    ```
