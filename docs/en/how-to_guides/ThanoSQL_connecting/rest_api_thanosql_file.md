---
title: How to Upload & Delete a File from the ThanoSQL Workspace Database
---

# __How to Upload a File to the ThanoSQL Workspace Database__

You can use ThanoSQL's REST API to remotely send and upload files to your ThanoSQL storage and insert them into any of your table within the database.

!!! warning "__Warning__"
    - File API supports image, audio, and video data with the following extensions:
        - Image:  "jpg", "png", "jpeg"
        - Audio: "mp3", "wav
        - Video: "mp4", "wmv", "avi"

## File Upload  

In order to upload a file only, use the below methods to send a file to the ThanoSQL storage. 

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/file/upload/"

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

## File Upload & Insert 

If "db commit" is set to True and "table name" and "column name" are specified, the given file is sent to ThanoSQL storage and the file path is inserted into a column of the specified table.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/file/upload/"
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

!!! faq "FAQ"
    - In order to use a file within the Jupyter workspace, you must put '/'home/jovyan' in front of the path. 


# __How to Delete a File From the ThanoSQL Workspace Database__

You can use ThanoSQL's REST API to delete files from your ThanoSQL storage and remove all rows from any of your tables within the database that have the same value as the file paths.

## File Delete

In order to delete a file only, use the below methods to delete a file from the ThanoSQL storage. 


=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/file/delete/"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'file_path': 'Data File Path'
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/file/delete/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"file_path": "Data File Path"}'
    ```


## File Delete & Remove 

If "db_commit" is set to True and "table_name" and "column_name" are specified, the given file is deleted from ThanoSQL storage, and all rows with the same value as the specified file path are removed from the table's specified column.


== "Python"

    ```python 
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/file/delete/"
    db_commit = True 

    api_url = f"{base_url}?db_commit={db_commit}"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'file_path': 'File Path',
        'table_name': 'Table Name',
        'column_name': 'Column Name'
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/file/delete/?db_commit=True' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"file_path": "Data File Path", "table_name": "Table Name", "column_name": "Column Name"}'
    ```
