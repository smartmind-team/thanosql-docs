---
title: Table APIs
---

# **Table APIs**

You can use the ThanoSQL Table REST API for several CRUD operations on your ThanoSQL DB tables.

!!! Note "__Table Object__"
    A table object consists of four main componenets:

    1. *`name`: The name of the table
    2. *`schema`: The schema that the table is a part of
    3. `columns`
        - `id`: The ordinal position of the column
        - `default`: The default value of the column
        - `is_nullable`: A boolean determining if the column is nullable
        - `type`: [Data type](https://www.postgresql.org/docs/current/datatype.html#:~:text=Table%C2%A08.1.%C2%A0Data%20Types) of the column
        - `name`: The name of the column
    4. `constraints`
        - `primary_key`: The primary key of the table 
            - `name`: The constraint name of the primary key
            - `columns`: The columns that make up the primary key
        - `foreign_keys`: 
            - `name`: The constraint name of the foreign key
            - `reference_schema`: The schema that contains the `reference_table`
            - `reference_table`: The table that contains the `reference_column`
            - `reference_column`: The column that the foreign key is referring to
            - `column`: The foreign key column

    _The above components that have a * next to its name will not be included in the body for the POST table API. Instead, it will be specified in the URL as query parameters._


## **`GET` /table**

In order to get a list of all of your tables, use the method below. If no `schema` is provided, then tables from every schemas will be listed.

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

Use this method to get the objects of a single table. If no `schema` query parameter is provided, the parameter defaults to the public schema.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
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

The ALTER Table API is used to do several ALTER TABLE operations. In order to alter the table you simply alter the database object specified by the `table_name` and `schema`. To UPDATE something, simply change the value of the Table object. To DROP, just remove the object from the request body.

!!! note "__Order Execution__"
    The order of execution of the ALTER is as follows:

    DROP PRIMARY KEY -> DROP FOREIGN KEY -> DROP COLUMN -> ADD COLUMN -> ALTER COLUMN -> RENAME COLUMN -> ADD PRIMARY KEY -> ADD FOREIGN KEY -> RENAME TABLE -> SET SCHEMA


!!! warning ""
    When dealing with columns, it is important to note that the id is the unique key that is used to identify the column. If this value is deleted, that means that the column will also be deleted. If you are making a change to the column, **make sure that the column id is present in the body**! When adding a new column, a column_id is not required as it will be assigned after the column is created.

In the following example lets pretend we want to alter the table object below:

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
  If no `schema` query parameter is provided, the parameter defaults to the public schema.


## **`POST` /table/{table_name}**

Use this method to execute the CREATE TABLE operation. In order to create the table you simply pass in a database object as a body with the `table_name` and `schema` as query params.

!!! note " "
    When adding Column objects to the list of columns, there is no need to specify the id since the id just refers to the ordinal position of the column. Additionally if the table is created with an empty body, an empty table will be created. If no table_name is specified, the table will be created with a random uuid string.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema = "Schema Name"

    # Note that the name and schema keys are missing from the body
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

To delete a table, use the method below. If no `schema` query parameter is provided, the parameter defaults to the public schema.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table"
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

Retrieves the paginated records of a table in a schema.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table"
    table_name = "Table name"
    schema = "Target schema"
    offset = "Offset"
    limit = "Limit"

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

- `table_name`: The table name to retrieve the records from.
- `schema`: The schema to search the table in (defaults to 'public').
- `offset`: The offset to where the pagination count will start from (defaults to 0).
- `limit`: The maximum number of records to retrieve starting from the offset (defaults to 100, max 100).

## **`POST` /table/{table_name}/records**

Inserts row(s) of new records into a table in a schema.

!!! note " "
    If the (existing) table columns and inserted row(s) don't match, the API will throw an error. Furthermore, since Postgres automatically converts all column names to lowercase, you can only use the API if all column names in the table are lowercase.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema = "Schema Name"

    # here we are inserting three rows
    data = [
                {
                    "user_id": 1,
                    "username": "abc,
                    "password": "abc123"
                },
                {
                    "user_id": 2,
                    "username": "def,
                    "password": "def456"
                },
                {
                    "user_id": 3,
                    "username": "ghi,
                    "password": "ghi789"
                }
            ]

    api_url = f"{base_url}/{table_name}/records?schema={schema}"

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
    'https://{your-engine-url}/api/v1/table/{table_name}/records?schema={schema}' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -d '[
            {
                "user_id": 1,
                "username": "abc,
                "password": "abc123"
            },
            {
                "user_id": 2,
                "username": "def,
                "password": "def456"
            },
            {
                "user_id": 3,
                "username": "ghi,
                "password": "ghi789"
            }
        ]'
    ```


## **`GET` /table/{table_name}/records/csv**

Retrieves the records of a table in a schema as a CSV file.

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


## **`POST` /table/{table_name}/upload/csv**

Inserts records from a CSV file into a table in a schema.

!!! Note " "
    Unlike most other methods, this method uses `multipart/form-data` instead of `application/json` as its encoding in order to facilitate a file upload. While a file is required, a body is not. The structure of the table will be inferred from the file in case a body is not provided. When provided, the API will use the body as a base for the table structure. If the file and the body does not match, the API will throw an error.


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema = "Schema Name"
    if_exists = "What to do if table of the same name already exists (one of fail, append, or overwrite)"

    file_name = "CSV file to be uploaded"
    data = {
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

    csv_files = {
        "file": (
            file_name,
            open(file_name),
            "text/csv",
        )
    }
    csv_files["body"] = (
            None,
            json.dumps(data),
            "application/json",
    )

    api_url = f"{base_url}/{table_name}/upload/csv?schema={schema}&if_exists={if_exists}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header, files=csv_files):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'POST' \
    'https://{your-engine-url}/api/v1/table/{table_name}/upload/csv?schema={schema}&if_exists={if_exists}' \
    -H 'accept: application/json' \
    -H 'Content-Type: multipart/form-data' \
    -F 'file=@file_name;type=text/csv'
    -F 'body={
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
        }'
    ```


## **`POST` /table/{table_name}/upload/excel**

Inserts records from an Excel-like file (xls, xlsx, xlsm, xlsb, odf, ods and odt) into a table in a schema. Works the same way as uploading CSV file.

!!! Note " "
    Pay attention to the different types of Excel file while specifying the content file type. Refer fo [this page](https://zappysys.zendesk.com/hc/en-us/articles/360034303774-Which-Content-Type-is-used-for-Multi-Part-Upload-File-Extension), for instance, for reference.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    table_name = "Table Name"
    base_url="https://{your-engine-url}/api/v1/table/"
    schema = "Schema Name"
    if_exists = "What to do if table of the same name already exists (one of fail, append, or overwrite)"

    file_name = "Excel file to be uploaded"

    # in case of xlsx and no body
    excel_files = {
        "file": (
            file_name,
            open(file_name),
            "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
        )
    }

    api_url = f"{base_url}/{table_name}/upload/excel?schema={schema}&if_exists={if_exists}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header, files=excel_files):
    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
      curl -X 'POST' \
    'https://{your-engine-url}/api/v1/table/{table_name}/upload/excel?schema={schema}&if_exists={if_exists}' \
    -H 'accept: application/json' \
    -H 'Content-Type: multipart/form-data' \
    -F 'file=@file_name;type=application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    -F 'body='
    ```