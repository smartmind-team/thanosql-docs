---
title: ThanoSQL Catalog APIs
---

# **ThanoSQL Catalog APIs**


## __`query` getCatalogData__

Lists all publicly available ThanoSQL models, datasets, and tables.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    catalog_name = "Name of catalog"  # optional
    id = "ID of catalog data"  # optional
    search = "Search keyword(s)"  # optional
    type = "Type of catalog data (model/dataset/table)"  # optional
    api_url = "https://{your-engine-url}/api/v1/catalog/thanosql"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    # Choose which parameters and fields you need (for full list, see below)
    query = f"""query {{
        getCatalogData(search: "{search}", type: "{type}") {{
            name
            type
            importQuery
        }}
    }}
    """

    r = requests.post(api_url, json={"query": query}, headers=header)
    # No need to raise for status as GraphQL requests always return a 200 status code
    r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://{your-engine-url}/api/v1/catalog/thanosql' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query": "query{getCatalogData(search: \"{search}\", type: \"{type}\"){name type importQuery}}"}'
    ```

### __Parameters__

The API can be called with or without any of the following parameters. Each parameter adds a filter to the query result. For example, setting `catalogName` to "tutorial" will tell the API to return only results with "tutorial" as their `catalogName`.

- `catalogName`: The name of the catalog the data belongs to.
- `id`: The id number of the catalog data.
- `search`: Keyword(s) that the catalog data names must contain.
- `type`: The type of the catalog data (must be either empty or one of "model", "dataset", and "table").

### __Response__

The following lists all attributes of a catalog data. When calling the API, select at least one of the available attributes. Only values of the selected attributes will appear in the response.

- `catalogName`: The name of the catalog the data belongs to.
- `createdAt`: The date of creation of the catalog data.
- `dataType`: The data type of the catalog data (only applies to models and datasets).
- `description`: Brief description of the catalog data.
- `id`: The id number of the catalog data.
- `importQuery`: The query statement used to import the catalog data to workspace.
- `modelClassName`: The name of the model class (only applies to models).
- `modelType`: The type of the model (only applies to models).
- `name`: The name of the catalog data.
- `s3Bucket`: The S3 bucket where the data is hosted (only applies to models and datasets).
- `s3Key`: The S3 key of the catalog data (only applies to models and datasets).
- `schemaName`: The name of the schema the table belongs to (only applies to tables).
- `tableType`: The type of the table (view or table, only applies to tables).
- `type`: The type of the catalog data (model, dataset, or table).
- `updatedAt`: The last date of update of the catalog data.
- `version`: The model version (only applies to models).
