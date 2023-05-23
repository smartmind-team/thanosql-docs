---
title: Query APIs
---

# **Query APIs**

Execute ThanoSQL queries and receive a query log as a response.

!!! Note "__Query Log__"
    A query log contains information about the query that was executed. Below are the attributes that make up a query log.

    - `query_id`: A unique ID that identifies the query.
    - `statement_type`: The query type (ex. SELECT, BUILD, etc.).
    - `start_time`: The start time of the query execution.
    - `end_time`: The end time of the query execution.
    - `query`: The query string that was executed.
    - `referer`: Indicates where the REST API call was made from.
    - `state`: Shows the state of the query execution (Either RUNNING or COMPLETE).
    - `destination_table`: Shows table where the results are stored in (If the `statement_type` is a SELECT, a new `destination_table` is created. Otherwise, the `destination_table` shows the affected table).
    - `error_result`: Stores the error message if there was an error executing the query.
    - `created_at`: Shows the timestamp of when the query log was created.

!!! warning "__Warning__"
    - Columns created using "__CONVERT__" are encoded using base64. To use it as a column containing bytes, it must be decoded using base64's b64decode.

## __`POST` /query__

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://{your-engine-url}/api/v1/query/"
    query="Query to request"
    query_type="SQL query type" #psql or thanosql

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'query_string': query, 'query_type': query_type
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()

    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/query/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_string": query, "query_type": query_type}'
    ```
