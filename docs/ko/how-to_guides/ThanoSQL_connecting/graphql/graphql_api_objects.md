---
title: GraphQL Objects
---

# **GraphQL Objects**


## __PublicCatalogData__

공용 ThanoSQL 모델, 데이터셋 및 테이블 데이터입니다.

### __Fields__

- `catalogName`: 데이터가 속한 카탈로그의 이름.
- `createdAt`: 카탈로그 데이터의 생성 날짜.
- `dataType`: 카탈로그 데이터의 데이터 유형(모델 및 데이터셋에만 적용됨).
- `description`: 카탈로그 데이터에 대한 간략한 설명.
- `id`: 카탈로그 데이터의 ID.
- `importQuery`: 카탈로그 데이터를 워크스페이스로 가져오는 데 사용되는 쿼리 문.
- `modelClassName`: 모델 클래스의 이름(모델에만 적용됨).
- `modelType`: 모델의 유형(모델에만 적용됨).
- `name`: 카탈로그 데이터의 이름.
- `s3Bucket`: 데이터가 호스팅되는 S3 버킷(모델 및 데이터셋에만 적용됨).
- `s3Key`: 카탈로그 데이터의 S3 키(모델 및 데이터셋에만 적용됨).
- `schemaName`: 테이블이 속한 스키마의 이름(테이블에만 적용됨).
- `tableType`: 테이블의 유형(뷰 또는 테이블, 테이블에만 적용됨).
- `type`: 카탈로그 데이터의 유형('model', 'dataset', 또는 'table').
- `updatedAt`: 카탈로그 데이터의 마지막 업데이트 날짜.
- `version`: 모델 버전(모델에만 적용됨).
