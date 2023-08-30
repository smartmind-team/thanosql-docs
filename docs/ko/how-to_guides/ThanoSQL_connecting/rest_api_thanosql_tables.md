---
title: Table APIs
---

# **Table APIs**

Table APIs로 ThanoSQL 워크스페이스 데이터베이스 테이블에 여러 CRUD 작업을 수행할 수 있습니다.

!!! Note "__테이블 객체__"
    테이블 객체는 네 가지 주요 구성 요소로 구성됩니다:

    1. *`name`: 테이블의 이름
    2. *`schema`: 테이블이 속한 스키마
    3. `columns`
        - `id`: 컬럼의 서수 위치
        - `default`: 컬럼의 기본값
        - `is_nullable`: 해당 컬럼이 널(null)을 허용하는지 여부를 나타내는 부울(boolean)
        - `type`: 컬럼의 [데이터 타입](https://www.postgresql.org/docs/current/datatype.html#:~:text=Table%C2%A08.1.%C2%A0Data%20Types)
        - `name`: 컬럼의 이름
    4. `constraints`
        - `primary_key`: 테이블의 기본키
            - `name`: 기본키의 제약 조건 이름
            - `columns`: 기본 키를 구성하는 컬럼
        - `foreign_keys`: 
            - `name`: 외래키의 제약 조건 이름
            - `reference_schema`: `reference_table`을 포함하는 스키마
            - `reference_table`: `reference_column`을 포함하는 테이블
            - `reference_column`: 외래키가 참조하는 컬럼
            - `column`: 외래키 컬럼

    _이름 옆에 *가 붙은 위의 구성 요소는 `POST` Table API의 요청 본문에는 포함되지 않습니다. 대신 URL에 쿼리 매개변수로 지정됩니다._


## **`GET` /table**

모든 테이블의 목록을 가져오려면 아래 메서드를 사용합니다. `schema`를 지정하지 않으면 모든 스키마의 테이블이 표시됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/table"
    schema = "Schema Name"

    api_url = f"{base_url}?schema={schema}"

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

단일 테이블 객체를 가져오려면 이 메서드를 사용합니다. `schema`의 기본 값은 public 입니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url = "https://{your-engine-url}/api/v1/table"
    schema = "Schema Name"

    api_url = f"{base_url}/{table_name}?schema={schema}"

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

ALTER Table API는 여러 ALTER TABLE 작업을 수행하는 데 사용됩니다. 테이블을 변경하려면 `table_name` 및 `schema`로 지정된 '테이블 객체'를 변경합니다. UPDATE하려면 테이블 객체의 값을 수정하고, DROP하려면 요청 본문에서 해당 개체를 제거하면 됩니다. `schema`의 기본 값은 public 입니다.

!!! note "__실행 순서__"
    ALTER의 실행 순서는 다음과 같습니다:

    DROP PRIMARY KEY -> DROP FOREIGN KEY -> DROP COLUMN -> ADD COLUMN -> ALTER COLUMN -> RENAME COLUMN -> ADD PRIMARY KEY -> ADD FOREIGN KEY -> RENAME TABLE -> SET SCHEMA


!!! warning ""
    컬럼을 다룰 때 `id`는 열을 식별하는 데 사용되는 고유 키라는 점에 유의하는 것이 중요합니다. 이 값이 삭제되면 컬럼도 삭제된다는 의미입니다. 컬럼을 변경하는 경우 **컬럼 `id`가 본문에 있는지 확인하세요**! 새 컬럼을 추가할 때는 컬럼이 생성된 후 할당되므로 컬럼 id는 필요하지 않습니다.

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
    base_url = "https://{your-engine-url}/api/v1/table"
    schema = "Schema Name"

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

    api_url = f"{base_url}/{table_name}?schema={schema}"

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
    'https://{your-engine-url}/api/v1/table/{table_name}?schema={schema}' \
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

## **`POST` /table/{table_name}**

이 메서드를 사용하여 CREATE TABLE 작업을 실행합니다. 테이블을 생성하려면 테이블 객체를 본문으로 전달하고 쿼리 매개변수로 `table_name` 및 `schema`을 전달합니다. `schema`의 기본 값은 public 입니다.

!!! note " "
    컬럼 객체를 열 목록에 추가할 때 id는 열의 서수 위치만 참조하므로, id를 지정할 필요가 없습니다. 또한 비어있는 본문(Body)은 빈 테이블이 생성됩니다. `table_name`을 지정하지 않으면 임의의 uuid 문자열을 이름으로 하는 테이블이 생성됩니다.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url = "https://{your-engine-url}/api/v1/table"
    schema = "Schema Name"

    # 본문(Body)에서 이름 및 스키마가 누락되었습니다.
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

    api_url = f"{base_url}/{table_name}?schema={schema}"

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
    'https://{your-engine-url}/api/v1/table/{table_name}?schema={schema}' \
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

테이블을 삭제하려면 아래 메서드를 사용합니다. `schema` 쿼리 매개변수가 제공되지 않으면 기본적으로 public 스키마로 설정됩니다.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url = "https://{your-engine-url}/api/v1/table"
    schema = "Schema Name"

    api_url = f"{base_url}/{table_name}?schema={schema}"

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


## **`GET` /table/{table_name}/records**

스키마에서 테이블의 paginated 된 데이터를 가져옵니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table"
    table_name = "Table name"
    schema = "Target schema"
    offset = {Offset}
    limit = {Limit}

    api_url = f"{base_url}/{table_name}/records?schema={schema}&offset={offset}&limit={limit}"

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
      'https://{your-engine-url}/api/v1/table/{table_name}/records?schema={schema}&offset={offset}&limit={limit}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```


### __Parameters__

- `table_name`: 레코드를 검색할 테이블 이름입니다.
- `schema`: 테이블을 검색할 스키마입니다(기본값은 'public').
- `offset`: 쿼리 로그를 조회를 시작할 기준점 (기본값: 0).
- `limit`: offset에서 시작하여 조회 할 결과의 개수 (기본값: 100, 최대 100).


## **`GET` /table/{table_name}/records/csv**

스키마에 있는 테이블의 레코드를 CSV 파일로 가져옵니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table"
    table_name = "Table name"
    timezone_offset = "Timezone offset from GMT (default: 9 (GMT+9, Seoul time))"

    api_url = f"{base_url}/{table_name}/records/csv?schema={schema}&timezone_offset={timezone_offset}"

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
      'https://{your-engine-url}/api/v1/table/{table_name}/records/csv?schema={schema}&timezone_offset={timezone_offset}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```
