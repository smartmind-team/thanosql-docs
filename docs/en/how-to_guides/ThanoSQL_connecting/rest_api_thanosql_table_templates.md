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

Retrieves a list of all of available table templates in a certain order. Optionally can include search keywords as well.

### __Parameters__

- `search`: Word(s) that the names of queried table templates must contain.
- `order_by`: How the query results should be ordered, there are three possible values: 'recent', based on date of creation from latest to oldest, 'name_asc', based on name from A to Z, and 'name_desc', based on name from Z to A.
- `latest`: A boolean value that can be set to `True` if you only want to return the latest version of each table template. Otherwise, it returns all available versions (default: `False`).

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/table_template/"
    search = "Search keyword(s)"
    order_by = "Order by"
    latest = {Latest}

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```


## **`GET` /table_template/{table_template_name}**

Use this method to get the table template object of a certain name. If needed, a specific or latest version can also be added to the request.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table_template"
    table_template_name = "Table template name"
    version = "A specific version, or 'latest' to request the latest table template"

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```

### __Response__

This API's response has the following structure:

```
{
  "table_templates": ["list of all table templates with the requested name, or one table template if version is not empty"]
  "versions": ["list of all available versions"]
}
```


## **`POST` /table_template/{table_template_name}**

Use this method to create a new table template.

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


## **`DELETE` /table_template/{table_template_name}**

Use this method to delete table templates of a certain name. If needed, it is also possible to delete only a specific version.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/table_template"
    table_template_name = "Table template name"
    version = "A specific version of the table template"

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```
