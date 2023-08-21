---
title: Schema APIs
---

# **Schema APIs**


## __`POST` /schema__

새 데이터베이스 스키마를 생성합니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/schema/"
    schema = "Name of schema to be created"

    api_url = f"{base_url}?schema={schema}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/schema/?schema={schema}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -d ''
    ```
    