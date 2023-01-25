---
title: How to Query using ThanoSQL
---

# **How to Query Using ThanoSQL**

You can use the same queries for ThanoSQL's REST API as you would in the ThanoSQL workspace.

!!! warning "__Warning__"
    - Columns created using "__CONVERT__" are encoded using base64. To use it as a column containing bytes, it must be decoded using base64's b64decode. 

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/query/"
    query="Query to request"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'query_string': query
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()

    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/query/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_string": query}'
    ```
