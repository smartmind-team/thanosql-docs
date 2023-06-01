---
title: Query APIs
---

# **Query APIs**


## __`POST` /query__

Execute ThanoSQL queries and receive a query log as a response.

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


### __Parameters__
    
The API can set a destination table to save the results from a query using query parameters. If nothing is specified, then a destination table will be created in the qm schema.


- `schema`: The schema to retrieve the tables from. If no parameter is provided, defaults to "qm".
- `table_name`: The name that will be used to create the table. If not parameter is provided, defaults to a randomly generated table name 
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
