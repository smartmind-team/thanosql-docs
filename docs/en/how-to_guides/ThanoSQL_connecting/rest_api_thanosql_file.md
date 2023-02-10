---
title: How to Upload & Delete a File from the ThanoSQL Workspace Database
---

# __How to Upload & Delete a File from the ThanoSQL Workspace Database__
## __How to Upload a File to the ThanoSQL Workspace Database__

You can use ThanoSQL's REST API to remotely send and upload files to your ThanoSQL storage and insert them into any of your table within the database. The default root folder for all user files in ThanoSQL is set to "drive."

!!! warning "__Warning__"
    - File API supports image, audio, and video data with the following extensions:
        - Image:  "jpg", "png", "jpeg"
        - Audio: "mp3", "wav
        - Video: "mp4", "wmv", "avi"
        
    - Files with extensions not listed above are stored in the "drive/others" folder.

### __File Upload__

In order to upload a file only, use the below methods to send a file to the ThanoSQL storage.
When the "dir=folder name" is added to the URL, the file will be uploaded to the 
designated folder.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url = "https://engine.thanosql.ai/api/v1/file/upload/"
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
      'https://engine.thanosql.ai/api/v1/file/upload/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Data File Path;type=file_type/Data File Type'
    ```

### __File Upload & Insert__

If "db commit" is set to True and "table name" and "column name" are specified, the given file is sent to ThanoSQL storage and the file path is inserted into a column of the specified table.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://engine.thanosql.ai/api/v1/file/upload/"
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
      'https://engine.thanosql.ai/api/v1/file/upload/?db_commit=True&table_name=Table name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Data File Path;type=file_type/Data File Type'
    ```

!!! faq "__FAQ__"
    - In order to use a file within the Jupyter workspace, you must put '/'home/jovyan' in front of the path. 


## __How to Delete a File from the ThanoSQL Workspace Database__

You can use ThanoSQL's REST API to delete files from your ThanoSQL storage and remove all rows from any of your tables within the database that have the same value as the file paths.

### __File Delete__

In order to delete a file only, use the below methods to delete a file from the ThanoSQL storage. 


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    file_path = "Data File Path"
    api_url = f"https://engine.thanosql.ai/api/v1/file/delete/?file_path={file_path}'

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
      'https://engine.thanosql.ai/api/v1/file/delete/?file_path=Data File Path' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```


### __File Delete & Remove__

If "db_commit" is set to True and "table_name" and "column_name" are specified, the given file is deleted from ThanoSQL storage, and all rows in the table with the same value as the specified file path in the specified column will be deleted.


=== "Python"

    ```python 
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url = "https://engine.thanosql.ai/api/v1/file/delete/"
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
      'https://engine.thanosql.ai/api/v1/file/delete/?db_commit=True&file_path=File Path&table_name=Table Name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```


### __Get File List__

A list of files and folders is returned from a specified file path. The file path can be expressed 
using a regular expression.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    file_path = "File Path"
    api_url = f"https://engine.thanosql.ai/api/v1/file/list/?file_path={file_path}"

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
      'https://engine.thanosql.ai/api/v1/file/list/?file_path={File Path}' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json'
    ```
