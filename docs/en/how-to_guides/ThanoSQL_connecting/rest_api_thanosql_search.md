---
title: Search APIs
---

# **Search APIs**

You can use Search APIs to query and search for similar images within the ThanoSQL Workspace database using images, text, and built models.

## __`POST` /search/file__

To search for images using an image file, you can use the following endpoint.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/search/file/"
    table_name = "Table Name"
    model_name = "Model Name"
    column_name = "Column Name"

    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    files = {'file': open('Image File Path', 'rb')}

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, files=files, stream=True) as r:
        r.raise_for_status()
        with open("Path to the zip file to save", 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/search/file/?table_name=Table Name&model_name=Model Name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Image File Path;type=image/Image File Type'
    ```

## __`POST` /search/text__

To search for images using a text, you can use the following endpoint.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://{your-engine-url}/api/v1/search/text/"
    table_name = "Table Name"
    model_name = "Model Name"
    column_name = "Column Name"
    text = 'Text to search'


    ## WHEN SEARCHING WITH IMAGE
    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}&text={text}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, stream=True) as r:
        r.raise_for_status()
        with open("Path to the zip file to save", 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/search/text/?table_name=Table Name&model_name=Model Name&column_name=Column Name&text=Text to search' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -d ''
    ```
