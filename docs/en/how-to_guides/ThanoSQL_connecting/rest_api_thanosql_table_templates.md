---
title: Table Template APIs
---

# **Table Template APIs**

You can use the ThanoSQL Table Template REST API for several CRUD operations on your table templates.

!!! Note "__Table Template Object__"
    A table template object consists of five main componenets:

    1. `name`: The name of the table template
    2. `table_template`: The body of the table template, consisting of columns and constraints (see [the documentation on the table object](../rest_api_thanosql_tables) for more details)
    3. `version`: The version of the table template (currently, this value is immutable)
    4. `compatibility`: The compatibility of the table template (one of `backward`, `forward`, `full`, or `none`)
    5. `created_at`: The date of creation of the table template


## **`GET` /table_template**

In order to get a list of all of your table templates, use the method below.

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

Use this method to get the most recent table template object of a certain name.

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

Use this method to create a new table template.

!!! Note " "
    As of now, there is no way to update or delete created table templates.


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

    ## Create Table
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
