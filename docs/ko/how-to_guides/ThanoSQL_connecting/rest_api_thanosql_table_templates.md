---
title: Table Template APIs
---

# **Table Template APIs**

테이블 템플릿에서 여러 CRUD 작업에 ThanoSQL Table Template REST API를 사용할 수 있습니다.

!!! Note "__Table Template Object__"
    테이블 템플릿 개체는 다섯 가지 주요 구성 요소로 구성됩니다:

    1. `name`: 테이블 템플릿의 이름
    2. `table_template`: 테이블 템플릿의 본문으로, 열과 제약 조건으로 구성됩니다(자세한 내용은 [테이블 객체 문서](../rest_api_thanosql_tables) 참조하세요)
    3. `version`: 테이블 템플릿의 버전(현재 이 값은 불변)
    4. `compatibility`: 테이블 템플릿의 호환성(`backward`, `forward`, `full`, 또는 `none` 중 하나)
    5. `created_at`: 테이블 템플릿의 생성한 날짜


## **`GET` /table_template**

모든 테이블 템플릿의 목록을 가져오려면 아래 방법을 사용하세요.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://{your-engine-url}/api/v1/table_template"

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
      'https://{your-engine-url}/api/v1/table_template/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```


## **`GET` /table_template/{table_template_name}**

특정 이름의 가장 최신 테이블 템플릿 개체를 가져오려면 이 메서드를 사용합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table_template"
    table_template_name = "Table template name"

    api_url = f"{base_url}/{table_template_name}"

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
      'https://{your-engine-url}/api/v1/table_template/{table_template_name}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```


## **`POST` /table_template/{table_template_name}**

이 메서드를 사용하여 새 테이블 템플릿을 만듭니다.

!!! Note " "
    현재로서는 생성된 테이블 템플릿을 업데이트하거나 삭제할 수 있는 방법이 없습니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/table_template/"
    table_template_name = "Table template name"

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
