---
title: Schema APIs
---

# **Schema APIs**


## **`GET` /schema**

In order to get a list of all of your schemas from the database, use the method below.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/schema/"

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
      'https://{your-engine-url}/api/v1/schema/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN'
