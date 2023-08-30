---
title: COPY
---

# __COPY__

## __1. COPY Statement__

The "__COPY__" statement allows users to create data tables in the ThanoSQL workspace database with their data files, data folders, and Pandas DataFrame within their workspace.

!!! note "__Supported File Types__"
    - csv
    - pkl, pickle
    - xls, xlsx, xlsm, xlsb
    - pqt, parquet 

## __2. COPY Syntax__

```sql
COPY (table_name_expression)
OPTIONS (
    expression [ , ...]
    )
FROM {file_path | dir_path}
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.
        - "if_exists": determines how the function should handle the case where the table already exists, it can either raise an error, append to the existing table, or replace the existing table (str, optional, 'fail'|'replace'|'append', default: 'fail')
            - "fail": raise an error if there is already an existing table
            - "replace": replace a table if there is already an existing table
            - "append": append given dataframe to an existing table

## __3. COPY Example__

### __3-1. Using the Path of the Data File__

The example below demonstrates how to use a data file for the "__COPY__" clause. A specified file with a path as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

```sql
%%thanosql
COPY mytable
OPTIONS (if_exists='replace')
FROM 'data/example.csv'
```

### __3-2. Using the Path of the Data Folder__

The example below demonstrates how to use a data directory for the "__COPY__" clause. A specified folder with a path as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

!!! note "__Using COPY with Data Folders__"
    - If the path to the folder containing the images, audios, or videos is given as an input, the “__COPY__” clause will translate each file as a row and recreate it as a data table.

```sql
%%thanosql
COPY mytable
OPTIONS (if_exists='replace')
FROM 'diet_image_data/'
```

### __3-3. Using a Pandas DataFrame__
The example below demonstrates how to use a Pandas DataFrame for the "__COPY__" clause. A specified dataframe as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

#### Prepare a Pandas DataFrame 
```python
# create a Pandas DataFrame
df = pd.read_csv("./diet_image_data/sample.csv")
# df must to be converted to JSON and 'orient' must be specified as 'records' 
df_in_json = df.to_json(orient="records")

# use a f-string to wrap 'df_in_json' within the COPY clause 
copy_pandas_df = f'''
COPY mytable
OPTIONS (if_exists='replace')
FROM '{df_in_json}'
'''
```

#### COPY a Pandas DataFrame 

```sql
%thanosql $copy_pandas_df
```

!!! warning "__Warning__"
    - A Pandas DataFrame must be converted to JSON before being wrapped in the __COPY__ clause. 
    - Converted JSON variable must be enclosed with double quotes inside the __COPY__ clause. 
    - __${variable_name}__ should be followed by the __%thanosql__.