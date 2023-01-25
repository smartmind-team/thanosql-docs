---
title: ThanoSQL 구문 활용
---

# __ThanoSQL 구문 활용__

REST API를 사용하여 ThanoSQL 콘솔상에서 사용하던 쿼리들을 사용할수 있습니다. 

!!! warning "__Warning__"
    - "__CONVERT__"를 사용하여 만든 열은 JSON으로 포맷하기 위해 base64를 사용하여 인코딩됩니다. 바이트를 포함하는 열로 사용하려면 base64의 b64decode를 사용하여 디코딩해야 합니다. 

=== "Python"

    ```python
    import requests

    api_token = "발급받은_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/query/"
    query="요청할 쿼리"
    query_type="쿼리를 실행할 sql type" - psql or thanosql

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'query_string': query, 'query_type': query_type
    }

    r = requests.post(api_url, json=data, headers=header)

    r.raise_for_status()
    return_json = r.json()
    
    ```

=== "cURL"

    ```shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/query/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_string": query, "query_type": query_type}'
    ```

!!! faq "FAQ" 
    - Jupyter 내부의 path를 조회하기 위해서는 앞에 /home/jovyan 을 붙여야 합니다.