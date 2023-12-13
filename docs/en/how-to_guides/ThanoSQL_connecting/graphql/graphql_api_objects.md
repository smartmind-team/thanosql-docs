---
title: GraphQL Objects
---

# **GraphQL Objects**


## __PublicCatalogData__

Publicly available ThanoSQL model, dataset, or table data.

### __Fields__

- `catalogName`: The name of the catalog the data belongs to.
- `createdAt`: The created date of the catalog data.
- `dataType`: The data type of the catalog data (only applies to models and datasets).
- `description`: Brief description of the catalog data.
- `id`: The id number of the catalog data.
- `importQuery`: The query statement used to import the catalog data to the workspace.
- `modelClassName`: The name of the model class (only applies to models).
- `modelType`: The type of the model (only applies to models).
- `name`: The name of the catalog data.
- `s3Bucket`: The S3 bucket where the data is hosted (only applies to models and datasets).
- `s3Key`: The S3 key of the catalog data (only applies to models and datasets).
- `schemaName`: The name of the schema the table belongs to (only applies to tables).
- `tableType`: The table's type (view or table, only applies to tables).
- `type`: The catalog data's type (model, dataset, or table).
- `updatedAt`: The last updated date of the catalog data.
- `version`: The model version (only applies to models).
