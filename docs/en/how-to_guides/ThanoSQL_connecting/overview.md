---
title: Overview
---

ThanoSQL provides a comprehensive set of APIs that allow users to interact with the ThanoSQL Engine and perform various operations. It follows the standard HTTP methods and status codes to provide a simple and intuitive interface for interaction with the ThanoSQL Engine. The Engine uses two kinds of design paradigm: REST and GraphQL.

## __Engine URL__
The Engine URL (`https://{your-engine-url}`) is unique and provided to you in the workspace settings page. To make API requests to your workspace, you must use this URL only. If you have any trouble, please contact us.

## __Authentication__
All APIs require authentication to access your protected resources. You need to include your API Token in the request headers to authenticate your request. This Token can also be found in your workspace settings page. Add your header like the following:
```
Authorization: Bearer API_TOKEN
```

## __Additional Documentation__
You can view the full documentation of our REST API by visiting the Swagger UI at the URL `https://{your-engine-url}/docs`. The Swagger UI provides a user-friendly interface for exploring and interacting with our API endpoints, and includes detailed documentation on each endpoint, including input and output parameters, example requests and responses, and more.