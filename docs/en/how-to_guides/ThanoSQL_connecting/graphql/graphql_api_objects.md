---
title: GraphQL Objects
---

# **GraphQL Objects**


## __PublicCatalogData__

Publicly available ThanoSQL model, dataset, or table data.

### __Fields__

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
