---
title: ThanoSQL Catalog APIs
---

# **ThanoSQL Catalog APIs**


## __`query` getCatalogData__

공개적으로 사용 가능한 모든 ThanoSQL 모델, 데이터셋 및 테이블을 나열합니다.

=== "Python"

    ```python 
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    catalog_name = "카탈로그의 이름"  # 선택
    id = "카탈로그 데이터의 ID"  # 선택
    search = "검색 키워드"  # 선택
    type = "카탈로그 데이터의 타입 (model/dataset/table)"  # 선택
    api_url = "https://{your-engine-url}/api/v1/catalog/thanosql"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    # 필요한 매개변수와 필드를 선택합니다(전체 목록은 아래 참조해 주세요).
    query = f"""query {{
        getCatalogData(search: "{search}", type: "{type}") {{
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
      'https://{your-engine-url}/api/v1/catalog/thanosql' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query": "query{getCatalogData(search: \"{search}\", type: \"{type}\"){name type importQuery}}"}'
    ```

### __Parameters__

API는 다음 매개변수를 포함하거나 포함하지 않고 요청할 수 있습니다. 각 매개변수는 쿼리 결과에 필터를 추가합니다. 예를 들어 `catalogName`을 'tutorial'로 설정하면 `catalogName`이 'tutorial'인 결과만 반환하도록 API에 지시합니다.

- `catalogName`: 데이터가 속한 카탈로그의 이름입니다.
- `id`: 카탈로그 데이터의 ID 번호입니다.
- `search`: 카탈로그 데이터 이름에 포함되어야 하는 키워드입니다.
- `type`: 카탈로그 데이터의 유형입니다('model', 'dataset', 'table' 중 하나이거나 비어 있어야 함).

### __Response__

다음은 카탈로그 데이터의 모든 attribute을 나열한 것입니다. API를 사용할 때 attribute 중 하나 이상을 선택합니다. 선택한 attribute의 값만 response에 표시됩니다.

- `catalogName`: 데이터가 속한 카탈로그의 이름입니다.
- `createdAt`: 카탈로그 데이터의 생성 날짜입니다.
- `dataType`: 카탈로그 데이터의 데이터 유형입니다(모델 및 데이터셋에만 적용됨).
- `description`: 카탈로그 데이터에 대한 간략한 설명입니다.
- `id`: 카탈로그 데이터의 ID 번호입니다.
- `importQuery`: 카탈로그 데이터를 워크스페이스로 가져오는 데 사용되는 쿼리 문입니다.
- `modelClassName`: 모델 클래스의 이름입니다(모델에만 적용됨).
- `modelType`: 모델의 유형(모델에만 적용됨).
- `name`: 카탈로그 데이터의 이름입니다.
- `s3Bucket`: 데이터가 호스팅되는 S3 버킷입니다(모델 및 데이터셋에만 적용됨).
- `s3Key`: 카탈로그 데이터의 S3 키입니다(모델 및 데이터셋에만 적용됨).
- `schemaName`: 테이블이 속한 스키마의 이름입니다(테이블에만 적용됨).
- `tableType`: 테이블의 유형(뷰 또는 테이블, 테이블에만 적용됨).
- `type`: 카탈로그 데이터의 유형입니다('model', 'dataset', 또는 'table').
- `updatedAt`: 카탈로그 데이터의 마지막 업데이트 날짜입니다.
- `version`: 모델 버전입니다(모델에만 적용됨).
