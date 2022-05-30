# __ThanoSQL 쿼리 사용하기__
REST API를 사용하여 ThanoSQL 콘솔상에서 사용하던 쿼리들을 사용할수 있습니다. 

=== "Python"

    ``` python
    import requests
    import json

    api_token = "발급받은 API TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/query"
    query="요청할 쿼리"

    header = {
        "Authorization" : f"Bearer {api_token}"
    }

    data = {
        'query_string' : query
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()
    
    ```

=== "cURL"

    ``` shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/query/?table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은 API TOKEN' \
      -d '{"query_string": query}'
    ```
