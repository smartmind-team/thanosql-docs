---
title: How to use ThanoSQL syntax
---

# **How to use ThanoSQL syntax**

## Preface

- Updated Date : {{ git_revision_date_localized }}

You can use the REST API to use queries that were used on the ThanoSQL console.

=== "Python"

    ``` python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/query"
    query="Query to request"

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
      'https://engine.thanosql.ai/api/v1/query/?table_name=Table Name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -d '{"query_string": query}'
    ```
