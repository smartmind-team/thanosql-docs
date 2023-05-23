---
title: Overview
---
ThanoSQL REST API는 ThanoSQL 엔진과 상호 작용하고 다양한 작업을 수행할 수 있는 포괄적인 API 세트를 제공합니다. 표준 HTTP 메서드와 상태 코드를 따르며 ThanoSQL 엔진과의 상호 작용을 위한 간단하고 직관적인 인터페이스를 제공합니다.

## __Engine URL__
엔진 URL(https://{your-engine-url})은 워크스페이스마다 다르게 제공됩니다. 해당 워크스페이스로 API 요청을 하기 위해서는 반드시 이 고유한 URL을 사용해야 합니다. 엔진 URL은 워크스페이스 설정 페이지에서 확인 가능합니다. 문제가 발생할 경우 저희에게 연락해 주시기 바랍니다. 

## __Authentication__
REST API로 보호된 리소스에 액세스하기 위해 인증이 필요합니다. API 토큰을 요청 헤더에 포함하여 요청을 인증해야 합니다. 이 토큰은 워크스페이스 설정 페이지에서 확인할 수 있습니다. 다음과 같이 헤더를 추가하십시오:
```
Authorization: Bearer API_TOKEN
```

## __Additional Documentation__
ThanoSQL REST API의 전체 문서는 Swagger UI로 제공됩니다.`https://{your-engine-url}/docs`를 방문해주세요. Swagger UI는 사용자 친화적인 인터페이스로 API 엔드포인트를 확인하고 상호 작용할 수 있으며, 각 엔드포인트에 대한 자세한 문서를 제공합니다. 입력 및 출력 매개변수, 예시 요청 및 응답 등이 포함됩니다. 