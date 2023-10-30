---
title: Query APIs
---

# **Query APIs**


## __`POST` /query__

Execute ThanoSQL queries and receive a query log as a response. The query can be either typed directly or retrieved from an existing query template (with or without parameters).

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/query/"
    query_type = "SQL query type" #psql or thanosql

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    # For query without templates (pick 1 between this and below):
    query = "Query to request"

    data = {
        'query_string': query, 'query_type': query_type
    }

    # For query with templates (pick 1 between this and above):
    template_name = "Name of template to use"     # we don't need this line if we query by template_id
    template_id = "ID number of template to use"  # we don't need this line if we query by template_name
    parameters = "Mapping of parameters to fill in the template"

    data = {
        'query_type': query_type,
        'template_name': template_name,  # or `'template_id': template_id` if we query by template_id
        'parameters': parameters         # optional, depending on whether it is needed by the template or not
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
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_string": query, "query_type": query_type}'
      # or -d '{"query_type": query_type, "template_name": template_name, "parameters": parameters}'
    ```

!!! warning "Query String and Query Template"
    Query strings and templates (and/or parameters) should not be used together. If `query_string` appears together with at least one of `template_id`, `template_name`, and `parameters`, an error will be returned.

### __Parameters__

The API can set a destination table to save the results from a query using query parameters. If nothing is specified, then a destination table will be created in the qm schema.

- `schema`: The schema to retrieve the tables from. If no parameter is provided, defaults to "qm".
- `table_name`: The name that will be used to create the table. If not parameter is provided, defaults to a randomly generated table name.
- `overwrite`: Determines if the table shall be overwritten or not. Defaults to False.

### __Response__

The `/query` API returns a query log (shown below) as a response.

If the query statement yields results (statements such as SELECT, LIST), then the results are stored into a destination table. The destination table is specified by the `destination_table_name` and `destination_schema`. If the query produces no results, then the destination table indicates the table that was affected

!!! Note "__Query Log__"
    A query log contains information about the query that was executed. Below are the attributes that make up a query log.

    - `query_id`: A unique ID that identifies the query.
    - `statement_type`: The query type (ex. SELECT, BUILD, etc.).
    - `start_time`: The start time of the query execution.
    - `end_time`: The end time of the query execution.
    - `query`: The query string that was executed.
    - `referer`: Indicates where the REST API call was made from.
    - `state`: Shows the state of the query execution (Either RUNNING or COMPLETE).
    - `destination_table_name`: Shows the destination table's name.
    - `destination_schema`: Shows the destination table's schema.
    - `error_result`: Stores the error message if there was an error executing the query.
    - `created_at`: Shows the timestamp of when the query log was created.

!!! warning "__Warning__"
    - Columns created using "__CONVERT__" are encoded using base64. To use it as a column containing bytes, it must be decoded using base64's b64decode.


## **`GET` /query/log**

This method retrieves a paginated list of all query logs.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/log"
    search = "Search keyword(s)"
    offset = {Offset}
    limit = {Limit}

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```

### __Parameters__

- `search`: Word(s) that the returned query logs should contain.
- `offset`: The offset to where the pagination count will start from (defaults to 0).
- `limit`: The maximum number of items to retrieve starting from the offset (defaults to 100, max 100).


## **`GET` /query/template**

Retrieves all available query templates.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    offset = {Offset}
    limit = {Limit}
    search = "Search keyword(s)"
    order_by = "Order by"

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```

### __Parameters__

- `offset`: The offset to where the pagination count will start from (defaults to 0).
- `limit`: The maximum number of items to retrieve starting from the offset (defaults to 100, max 100).
- `search`: Word(s) that the names of returned query templates should contain (defaults to empty string).
- `order_by`: How the query results should be ordered, there are three possible values: 'recent', based on date of creation from latest to oldest, 'name_asc', based on name from A to Z, and 'name_desc', based on name from Z to A (defaults to 'recent').

### __Response__

This method returns a list of query templates (shown below) as a response.

!!! Note "__Query Template__"
    A query template contains a string that may or may not need some parameters in order to be complete. The templates, for both psql and thanosql, follow the conventions used by [Jinja](https://jinja.palletsprojects.com/en/3.1.x/). Below are the attributes that make up a query template.

    - `id`: A unique number that identifies the query template.
    - `name`: The query template name.
    - `query`: The query template string.
    - `created_at`: Shows the timestamp of when the query template was created.
    - `updated_at`: Shows the timestamp of when the query template was last modified.

    One example of templates that require parameters is:

    ```sql
    INSERT INTO {{ schema }}.\"{{ name }}\" ({% for column, _ in data.items() -%}{{ column }}{% if not loop.last %}, {% endif %}{%- endfor %})
    VALUES ({% for _, value in data.items() -%}{% if value is string %}'{{ value }}'{% else %}{{ value }}{% endif %}{% if not loop.last %}, {% endif %}{%- endfor %});
    ```

    The template above requires parameters `schema`, `name`, and `data`. The template below, on the other hand, is already complete and thus does not require parameters in order to be a valid query:

    ```sql
    GET THANOSQL DATASET diet_data
    OPTIONS (overwrite=True)
    ```


## **`POST` /query/template**

Creates a new query template with a certain name and query string.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/query/template"
    name = "Name of query template"
    query = "Query template string (in psql or thanosql)"

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
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"name": name, "query": query}'
    ```


## **`GET` /query/template/{template_name}**

Retrieves the query template with a certain name.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    template_name = "Name of query template"

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```


## **`PUT` /query/template/{template_name}**

Updates a query template with a certain name.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    template_name = "Name of query template"
    name = "Updated name of query template"
    query = "Updated query template string (in psql or thanosql)"

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
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"name": name, "query": query}'
    ```

!!! Note
    If you only want to update one field, leave the other field out of the data JSON. For example, if you only want to update the name of the query template, use `{"query": query}` as the data body.


## **`DELETE` /query/template/{template_name}**

Deletes a query template with a certain name.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/query/template"
    template_name = "Name of query template"

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
      -H 'Authorization: Bearer Issued_API_TOKEN'
    ```
