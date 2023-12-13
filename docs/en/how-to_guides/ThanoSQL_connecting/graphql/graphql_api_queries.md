---
title: GraphQL Queries
---

# **GraphQL Queries**


## __publicCatalogData__

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
    api_url = "https://{your-engine-url}/api/graphql"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    # Choose which arguments and fields you need (for full list, see below)
    query = f"""query {{
        publicCatalogData(search: "{search}", type: "{type}") {{
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
      'https://{your-engine-url}/api/graphql' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query": "query{publicCatalogData(search: \"{search}\", type: \"{type}\"){name type importQuery}}"}'
    ```

### __Arguments__

The API can be called with or without any of the following arguments. Each argument adds a filter to the query result. For example, setting `catalogName` to "tutorial" will tell the API to return only results with "tutorial" as their `catalogName`.

- `catalogName`: The name of the catalog the data belongs to.
- `id`: The id number of the catalog data.
- `search`: Keyword(s) that the catalog data names must contain.
- `type`: The type of the catalog data (must be either empty or one of "model", "dataset", and "table").

### __Response__

For the full list of fields in a catalog data object, please refer to the [PublicCatalogData Objects page](../graphql_api_objects/#publiccatalogdata). When calling the API, select at least one of the available field names. Only values of the selected fields will appear in the response.
