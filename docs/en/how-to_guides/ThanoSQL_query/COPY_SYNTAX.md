---
title: COPY
---

# __COPY__

## __1. COPY Statement__

The "__COPY__" statement allows users to create data tables in the ThanoSQL DB with their data files, data folders, and Pandas DataFrame within their workspace.

!!! note "__Supported file types__"
    - csv
    - pkl
    - xls, xlsx, xlsm, xlsb

!!! warning "__Warning__" 
    - Data files and dataframes' column names can only contain lowercase letters, numbers, and an underscore.

## __2. COPY Syntax__

The "__COPY__" statement creates data tables in the ThanoSQL DB with their data files, data folders, and dataframe within their workspace.

```sql
%%thanosql
COPY [custom_data_table_name] 
OPTIONS (
    overwrite=True
) 
FROM  
[absolute_file_path | absolute_dir_path | dataframe]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (DEFAULT: False)

## __3. COPY Example__

### __3-1. Using the path of the data file__

The example below demonstrates how to use a data folder for the COPY clause. A specified file with a path as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "data/example.csv"
```

### __3-2. Using the path of the data folder__

The example below demonstrates how to use a data directory for the COPY clause. A specified folder with a path as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

!!! note "__Using COPY with Data Folders"
    - If the path to the folder containing the images, audios, or videos is given as an input, the "__COPY__" clause will translate each image as a row and recreate it as a data table.

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "diet_image_data/"
```

### __3-2. Using a Pandas DataFrame__
The example below demonstrates how to use a Pandas DataFrame for the COPY clause. A specified dataframe as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 
#### Prepare a Pandas DataFrame 
```python
df = pd.read_csv("./diet_image_data/sample.csv")
df_in_json = df.to_json(orient="records")
```

#### Wrap it within the COPY clause 
```python 
copy_pandas_df = f'''
COPY mytable 
OPTIONS (
    overwrite=True
)
FROM '{df_in_json}'
'''
```

#### COPY a Pandas DataFrame 

```sql
%%thanosql $copy_pandas_df
```

!!! warning "__Warning__"
    - A Pandas DataFrame must be converted to JSON before being wrapped in the __COPY__ clause. 
    - __${variable_name}__ should be followed by the __%%thanosql__. 
