---
title: Table APIs
---

# **Table APIs**

ThanoSQL 테이블 REST API를 사용하여 ThanoSQL DB 테이블에 대한 여러 CRUD 작업을 수행할 수 있습니다.

!!! Note "__Table Object__"
    테이블 객체는 네 가지 주요 구성 요소로 구성됩니다:

    1. *`name`: 테이블의 이름
    2. *`schema`: 테이블이 속한 스키마
    3. `columns`
        - `id`: 열의 서수 위치
        - `default`: 열의 기본값
        - `is_nullable`: 열이 널화 가능한지 여부를 나타내는 부울
        - `type`: 열의 [데이터 타입](https://www.postgresql.org/docs/current/datatype.html#:~:text=Table%C2%A08.1.%C2%A0Data%20Types)
        - `name`: 열의 이름
    4. `constraints`
        - `primary_key`: 테이블의 기본 키
            - `name`: 기본 키의 제약 조건 이름
            - `columns`: 기본 키를 구성하는 열
        - `foreign_keys`: 
            - `name`: 외래 키의 제약 조건 이름
            - `reference_schema`: `reference_table` 포함하는 스키마
            - `reference_table`: `reference_column` 포함하는 테이블
            - `reference_column`: 외래 키가 참조하는 열
            - `column`: 외래 키 열

    _이름 옆에 *가 붙은 위의 구성 요소는 POST 테이블 API의 본문에 포함되지 않습니다. 대신 URL에 쿼리 매개변수로 지정됩니다._


## **`GET` /table**

모든 테이블의 목록을 가져오려면 아래 메서드를 사용합니다. `schema_name`을 제공하지 않으면 모든 스키마의 테이블이 표시됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/table"
    schema_name = "Schema Name"

    api_url = f"{base_url}?schema_name={schema_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## SEARCH WITH IMAGE
    r = requests.get(api_url, headers = header):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'GET' \
      'https://{your-engine-url}/api/v1/table/?schema=public' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```


## **`GET` /table/{table_name}**

단일 테이블의 개체를 가져오려면 이 메서드를 사용합니다. `schema_name` 제공되지 않으면 기본적으로 public 스키마로 설정됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema_name = "Schema Name"

    api_url = f"{base_url}/{table_name}?schema_name={schema_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## SEARCH WITH IMAGE
    r = requests.get(api_url, headers = header):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'GET' \
      'https://{your-engine-url}/api/v1/table/{table_name}/?schema=public' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```
  
## **`PUT` /table/{table_name}**

ALTER 테이블 API는 여러 ALTER 테이블 작업을 수행하는 데 사용됩니다. 테이블을 변경하려면 `table_name` 및 `schema_name`으로 지정된 데이터베이스 객체를 변경하기만 하면 됩니다. UPDATE하려면 테이블 객체의 값을 변경하기만 하면 됩니다. DROP하려면 요청 본문에서 해당 개체를 제거하면 됩니다.

!!! note "__Order Execution__"
    ALTER의 실행 순서는 다음과 같습니다:

    DROP PRIMARY KEY -> DROP FOREIGN KEY -> DROP COLUMN -> ADD COLUMN -> ALTER COLUMN -> RENAME COLUMN -> ADD PRIMARY KEY -> ADD FOREIGN KEY -> RENAME TABLE -> SET SCHEMA


!!! warning ""
    열을 다룰 때 id는 열을 식별하는 데 사용되는 고유 키라는 점에 유의하는 것이 중요합니다. 이 값이 삭제되면 열도 삭제된다는 의미입니다. 열을 변경하는 경우 **열 ID가 본문에 있는지 확인하세요**! 새 컬럼을 추가할 때는 컬럼이 생성된 후 할당되므로 column_id는 필요하지 않습니다.

다음 예제에서는 아래의 테이블 객체를 변경한다고 가정해 보겠습니다:
```json
{
    "table": {
        "name": "account",
        "schema": "public",
        "columns": [
        {
            "id": 1,
            "default": "nextval('accounts_user_id_seq'::regclass)",
            "is_nullable": false,
            "type": "integer",
            "name": "user_id"
        },
        {
            "id": 2,
            "default": null,
            "is_nullable": false,
            "type": "character varying",
            "name": "username"
        }],
        "constraints": {
        "primary_key": {
            "name": "accounts_pkey",
            "columns": [
            "user_id"
            ]
        },
        "foreign_keys": [
            {
                "name": "account_id_fkey",
                "reference_schema": "public",
                "reference_column": "role_id",
                "reference_table": "roles",
                "column": "user_id"
            }]
        }
    }
}
```


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema_name = "Schema Name"

    new_table = {
            "table": {
                "name": "account",
                "schema": "public",
                "columns": [
                {
                    "id": 1,
                    "default": "nextval('accounts_user_id_seq'::regclass)",
                    "is_nullable": False,
                    "type": "integer",
                    "name": "id"
                },
                {
                    "id": 2,
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
                }
                ],
                "constraints": {
                    "primary_key": {},
                    "foreign_keys": []
                }
            }
        }

    api_url = f"{base_url}/{table_name}?schema_name={schema_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## Alter Table
    r = requests.put(api_url, headers = header, body=new_table):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'PUT' \
    'https://{your-engine-url}/api/v1/table/{table_name}?schema={schema_name}' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -d '{
        "table": {
            "name": "account",
            "schema": "public",
            "columns": [
            {
                "id": 1,
                "default": "nextval('\''accounts_user_id_seq'\''::regclass)",
                "is_nullable": false,
                "type": "integer",
                "name": "id"
            },
            {
                "id": 2,
                "default": null,
                "is_nullable": true,
                "type": "character varying",
                "name": "username"
            },
            {
                "default": null,
                "is_nullable": false,
                "type": "character varying",
                "name": "password"
            }
            ],
            "constraints": {
                "primary_key": {},
                "foreign_keys": []
            }
        }
    }'
    ```
  `schema_name` 제공되지 않으면 기본적으로 public 스키마로 설정됩니다.


## **`POST` /table/{table_name}**

이 메서드를 사용하여 CREATE TABLE 작업을 실행합니다. 테이블을 생성하려면 데이터베이스 객체를 본문으로 전달하고 쿼리 매개변수로 `table_name` 및 `schema_name`을 전달하기만 하면 됩니다.

!!! note " "
    열 개체를 열 목록에 추가할 때 id는 열의 서수 위치만 참조하므로 id를 지정할 필요가 없습니다. 또한 테이블이 빈 본문으로 생성되는 경우 빈 테이블이 생성됩니다. `table_name`을 지정하지 않으면 임의의 uuid 문자열로 테이블이 생성됩니다.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema_name = "Schema Name"

    # 본문에서 이름 및 스키마 키가 누락되었습니다.
    new_table = {
            "table": {
                "columns": [
                {
                    "default": "nextval('accounts_user_id_seq'::regclass)",
                    "is_nullable": False,
                    "type": "integer",
                    "name": "user_id"
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
                }
                ],
                "constraints": {
                    "primary_key": {
                        "name": "accounts_pkey",
                        "columns": [
                        "user_id"
                        ]
                    },
                    "foreign_keys": [
                        {
                            "name": "account_id_fkey",
                            "reference_schema": "public",
                            "reference_column": "role_id",
                            "reference_table": "roles",
                            "column": "user_id"
                        }
                    ]
                }
            }
        }

    api_url = f"{base_url}/{table_name}?schema_name={schema_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## Create Table
    r = requests.post(api_url, headers = header, body=new_table):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'POST' \
    'https://{your-engine-url}/api/v1/table/{table_name}?schema={schema_name}' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -d '{
            "table": {
                "columns": [
                {
                    "default": "nextval('\''accounts_user_id_seq'\''::regclass)",
                    "is_nullable": false,
                    "type": "integer",
                    "name": "user_id"
                },
                {
                    "default": null,
                    "is_nullable": true,
                    "type": "character varying",
                    "name": "username"
                },
                {
                    "default": null,
                    "is_nullable": true,
                    "type": "character varying",
                    "name": "password"
                }
                ],
                "constraints": {
                    "primary_key": {
                        "name": "accounts_pkey",
                        "columns": [
                        "user_id"
                        ]
                    },
                    "foreign_keys": [
                        {
                            "name": "account_id_fkey",
                            "reference_schema": "public",
                            "reference_column": "role_id",
                            "reference_table": "roles",
                            "column": "user_id"
                        }
                    ]
                }
            }
        }'
    ```

## **`DELETE` /table/{table_name}**

테이블을 삭제하려면 아래 메서드를 사용합니다. `schema_name` 쿼리 매개변수가 제공되지 않으면 기본적으로 public 스키마로 설정됩니다.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table"
    schema_name = "Schema Name"

    api_url = f"{base_url}/{table_name}?schema_name={schema_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## DELETE Table
    r = requests.delete(api_url, headers = header):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'DELETE' \
      'https://{your-engine-url}/api/v1/table/{table_name}?schema=public' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```