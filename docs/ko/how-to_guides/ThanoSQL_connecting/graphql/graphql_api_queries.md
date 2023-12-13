---
title: GraphQL Queries
---

# **GraphQL Queries**


## __publicCatalogData__

공용 ThanoSQL 모델, 데이터셋 및 테이블을 나열합니다.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    catalog_name = "카탈로그 이름"  # 선택
    id = "카탈로그 데이터 ID"  # 선택
    search = "검색 키워드"  # 선택
    type = "카탈로그 데이터 타입 (model/dataset/table)"  # 선택
    api_url = "https://{your-engine-url}/api/graphql"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    # 필요한 인수와 필드를 선택합니다(전체 목록은 아래 참조해 주세요).
    query = f"""query {{
        publicCatalogData(search: "{search}", type: "{type}") {{
            name
            type
            importQuery
        }}
    }}
    """

    r = requests.post(api_url, json={"query": query}, headers=header)
    # GraphQL 요청은 항상 200 코드를 반환하므로 raise_for_status 필요가 없습니다.
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

API는 다음 인수들을 포함하거나 포함하지 않고 요청할 수 있습니다. 각 인수는 쿼리 결과에 필터를 추가합니다. 예를 들어 `catalogName`을 'tutorial'로 설정하면 `catalogName`이 'tutorial'인 결과만 반환하도록 API에 지시합니다.

- `catalogName`: 데이터가 속한 카탈로그 이름.
- `id`: 카탈로그 데이터의 ID.
- `search`: 카탈로그 데이터 이름에 포함되어야 하는 키워드.
- `type`: 카탈로그 데이터의 유형('model', 'dataset', 'table' 중 하나이거나 비어 있어야 함).

### __Response__

publicCatalogData 쿼리는 PublicCatalogData 개체를 반환합니다. 전체 필드 목록은 개체 페이지의 [PublicCatalogData 섹션](../graphql_api_objects/#publiccatalogdata)을 참조하세요. API를 호출할 때 선택 가능한 필드 이름 중 하나 이상을 선택하십시오. 선택한 필드의 값만 응답에 표시됩니다.
