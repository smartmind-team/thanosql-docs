---
title: File APIs
---

# **File APIs**

You can use File APIs to remotely send and upload files to your Workspace storage and insert them into any of your table within the database. The default root folder for all user files is set to "drive."

!!! warning "__Warning__"
    - File API supports image, audio, and video data with the following extensions:
        - Image:  "jpg", "png", "jpeg"
        - Audio: "mp3", "wav
        - Video: "mp4", "wmv", "avi"
        
    - Files with extensions not listed above are stored in the "drive/others" folder.

## __`POST` /file/upload__

In order to upload a file only, use the below methods to send a file to the Workspace storage.
When the "dir=folder name" is added to the URL, the file will be uploaded to the 
designated folder.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url = "https://{your-engine-url}/api/v1/file/upload/"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('Data File Path', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/upload/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Data File Path;type=file_type/Data File Type'
    ```

If "db commit" is set to True and "table name" and "column name" are specified, the given file is sent to Workspace storage and the file path is inserted into a column of the specified table.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/file/upload/"
    table_name = "Table Name"
    column_name = "Column Name"
    db_commit = True 

    api_url = f"{base_url}?db_commit={db_commit}&table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('Data File Path', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/upload/?db_commit=True&table_name=Table name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Data File Path;type=file_type/Data File Type'
    ```

!!! faq "__FAQ__"
    - In order to use a file within the Jupyter workspace, you must put '/'home/jovyan' in front of the path. 


## __`POST` /file/delete__

In order to delete a file only, use the below methods to delete a file from the Workspace storage. 

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    file_path = "Data File Path"
    api_url = f"https://{your-engine-url}/api/v1/file/delete/?file_path={file_path}'

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/delete/?file_path=Data File Path' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```


If "db_commit" is set to True and "table_name" and "column_name" are specified, the given file is deleted from Workspace storage, and all rows in the table with the same value as the specified file path in the specified column will be deleted.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://{your-engine-url}/api/v1/file/delete/"
    db_commit = True 
    file_path = 'File Path'
    table_name = 'Table Name'
    column_name = 'Column Name'

    api_url = f"{base_url}?db_commit={db_commit}&file_path={file_path}&table_name={table_name}&column_name={column_name}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/delete/?db_commit=True&file_path=File Path&table_name=Table Name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```


## __`POST` /file/list__

A list of files and folders is returned from a specified file path. The file path can be expressed using a regular expression.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    file_path = "File Path"
    api_url = f"https://{your-engine-url}/api/v1/file/list/?file_path={file_path}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    r = requests.post(api_url, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/file/list/?file_path={File Path}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```
