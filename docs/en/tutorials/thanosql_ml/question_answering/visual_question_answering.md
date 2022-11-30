---
title: Use the Visual Question Answering Model
--- 

# __Use the Visual Question Answering Model__

- Tutorial Difficulty: ★☆☆☆☆
- 5 min read
- Languages: [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location: tutorial_en/thanosql_ml/question_answering/visual_question_answering.ipynb
- References: [COCO Dataset](https://cocodataset.org/#download), [VQA](https://visualqa.org/index.html), [ViLT](https://arxiv.org/abs/2102.03334)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Visual Question Answering</h4>
    <p>Visual Q&A is a technique to find an appropriate answer to a question when an image(Visual) and a question about the image(Question) are given. Visual Q&A is a technology that can be used in many fields, such as children's learning and artificial intelligence assistants.</p>
</div>

Since its inception with CVPR in 2016, the [VQA Challenge](https://visualqa.org/challenge.html) has been held every year. During the challenge, advancements in VQA technologies are evaluated and awarded. Since 2017, many of the researchers have been rigorously evaluating the effectiveness of artificial intelligence with the collected data showing similar images and giving different answers to the same question as the VQA 2.0 dataset. For example, given a similar image to the question "Who is wearing glasses?", the distribution of the data is considered  so that the answer to the given question can either be 'male' or 'female'.

__The following are examples and applications of the ThanoSQL text-image search algorithm.__

- It can be used as an assistive system for the visually impaired in an online product sales service. When a customer who has difficulty checking the product image asks a question about the color or material of the product, the visual Q&A model can be used to reduce CS work by providing the answer by itself.

- It can be used to create a service that finds the information people need from similar images all at once by asking a single question.


<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 Since the following model can only be used for predictions, we will query how many people are in the given image and see the results using images from the COCO dataset.</p>
</div>


<div class="admonition tip">
    <h4 class="admonition-title">Dataset Description</h4>
    <p>The <code>COCO</code> dataset is a dataset created to be used for object detection, segmentation, and keypoint detection. This tutorial will only use the images from the "person" category (2,685 images) among the validation images section of the dataset.</p>
</div>

## __0. Prepare Dataset and Model__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET coco_person_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" downloads the specified dataset to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL DATASET</strong> clause.
        <ul>
            <li>"overwrite": determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY coco_person_data 
OPTIONS (overwrite=True)
FROM 'thanosql-dataset/coco_person_data/coco_person.csv'
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>COPY</strong>" specifies the name of the dataset to be saved as a database table. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>COPY</strong> clause.
        <ul>
           <li>"overwrite": determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>

### __Prepare the Model__


```python
%%thanosql
GET THANOSQL MODEL vilt
OPTIONS (overwrite=True)
AS tutorial_vilt
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details </h4>
    <ul>
        <li>"<strong>GET THANOSQL MODEL</strong>" downloads the specified model to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL MODEL</strong> clause.
        <ul>
            <li>"overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, default: False) </li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" names the given model. If not specified, the model will be named as the default <code>THANOSQL MODEL</code> name.</li>
    </ul>
</div>

## __1. Check Dataset__

To use the visual question-and-answer model, we use the `coco_person_data` table. To check the contents of the table, run the query below.


```python
%%thanosql
SELECT *
FROM coco_person_data
LIMIT 5
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image_path</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/coco_person_data/000000398905...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/coco_person_data/000000562243...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/coco_person_data/000000376307...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/coco_person_data/000000441586...</td>
      <td>person</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/coco_person_data/000000007281...</td>
      <td>person</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
   <h4 class="admonition-title">Understanding the Data</h4>
   <ul>
      <li><mark style="background-color:#D7D0FF ">image_path</mark>: the name of the column that stores the image path.</li>
      <li><mark style="background-color:#D7D0FF ">category</mark>: categories of images</li>
</div>


```python
%%thanosql
PRINT IMAGE 
AS
SELECT image_path 
FROM coco_person_data 
LIMIT 5
```

    /home/jovyan/thanosql-dataset/coco_person_data/000000398905.jpg



    
![jpeg](/en/img/tutorials/thanosql_ml/visual_question_answering/output_16_1.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000562243.jpg



    
![jpeg](/en/img/tutorials/thanosql_ml/visual_question_answering/output_16_3.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000376307.jpg



    
![jpeg](/en/img/tutorials/thanosql_ml/visual_question_answering/output_16_5.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000441586.jpg



    
![jpeg](/en/img/tutorials/thanosql_ml/visual_question_answering/output_16_7.jpg)
    


    /home/jovyan/thanosql-dataset/coco_person_data/000000007281.jpg



    
![jpeg](/en/img/tutorials/thanosql_ml/visual_question_answering/output_16_9.jpg)
    


## __2. Predict Response to a Question about the Images Using a Pretrained Model__

To predict the results using the previously trained visual question and answer model, <mark style="background-color:#E9D7FD ">tutorial_vilt</mark> model, run the query below.


```python
%%thanosql
PREDICT USING tutorial_vilt
OPTIONS (
    image_col='image_path',
    question='How many people are there?',
    column_name='predict_result'
    )
AS
SELECT image_path
FROM coco_person_data
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image_path</th>
      <th>predict_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/coco_person_data/000000398905...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/coco_person_data/000000562243...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/coco_person_data/000000376307...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/coco_person_data/000000441586...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/coco_person_data/000000007281...</td>
      <td>10</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2680</th>
      <td>thanosql-dataset/coco_person_data/000000008844...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2681</th>
      <td>thanosql-dataset/coco_person_data/000000321790...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2682</th>
      <td>thanosql-dataset/coco_person_data/000000166478...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2683</th>
      <td>thanosql-dataset/coco_person_data/000000122672...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2684</th>
      <td>thanosql-dataset/coco_person_data/000000163562...</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>2685 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" use the <mark style="background-color:#E9D7FD ">tutorial_vilt</mark> model loaded in the previous step via query syntax for prediction.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for prediction.
        <ul>
            <li>"image_col": the name of the column where the path of the image used for prediction is stored. (default: "image_path")</li>
            <li>"question": the question text to be used for prediction.</li>
            <li>"column_name": defines the name of the column to contain the result. (default: "predict_result")</li>
        </ul>
        </li>
    </ul>
</div>

## __3. In Conclusion__

In this tutorial, we used a visual question-and-answer model to predict outcomes by asking questions about images in the `COCO dataset`. As this is a beginner-level tutorial, we focused on getting visible results through simple queries. If you only ask a question about the images you need, you will get a value that is closer to the desired result.

* [How to Upload My Data to the ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/data_upload/)
* [How to Create a Table Using My Data](https://docs.thanosql.ai/en/how-to_guides/ThanoSQL_query/COPY_SYNTAX/)
* [How to Upload My Model to the ThanoSQL Workspace](https://docs.thanosql.ai/en/how-to_guides/ThanoSQL_query/UPLOAD_SYNTAX/)

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your service, please feel free to contact us below😊</p>
    <p>For inquiries regarding building visual question-and-answer models: contact@smartmind.team</p>
</div>
