---
title: Search Text by Text
---

# __Search Text by Text__

- Tutorial Difficulty : ★☆☆☆☆
- 7 min read 
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_search/search_text_by_text.ipynb   
- References : [Worrd Embeddings: LEXICAL SEMANTICS Encoding](https://pytorch.org/tutorials/beginner/nlp/word_embeddings_tutorial.html)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Text Vectorization</h4>
    <p>Computers cannot directly interpret human language (natural language). Therefore, a process for converting natural language into numerical data that can be recognized by computers is required. In the field of natural language processing, embedding refers to the result of converting human natural language into a vectorized format, which is a form that can be understood by machines.</p>
</div>

Techniques for converting natural language into embeddings are largely divided into statistical techniques and artificial neural network-based techniques. ThanoSQL provides a method to train a text vectorization model using self-supervised learning.

<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 Uses <mark style="background-color:#FFD79C">movie review data</mark>. The data consists of movie review texts and label values. However, because we are demonstrating self-supervised learning, this tutorial does not use label values. By training a model with 4,000 movie reviews, we will be able to search text by text and extract the importance of each word from a given movie review. </p>
</div>

## __0. Prepare Dataset__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET movie_review_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" downloads the specified dataset to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL DATASET</strong> clause.
        <ul>
            <li>"overwrite" : Determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY movie_review_train
OPTIONS (overwrite=True) 
FROM "thanosql-dataset/movie_review_data/movie_review_train.csv"
```

    Success



```python
%%thanosql
COPY movie_review_test 
OPTIONS (overwrite=True) 
FROM "thanosql-dataset/movie_review_data/movie_review_test.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>COPY</strong>" specifies the name of the dataset to be saved as a database table. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>COPY</strong> clause.
        <ul>
           <li>"overwrite" : Determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. Check Dataset__

To create a movie review text search model, we use the <mark style="background-color:#FFEC92 ">movie_review_train</mark> table from the ThanoSQL database. To check the table's contents, run the following query.


```python
%%thanosql
SELECT *
FROM movie_review_train
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
      <th>review</th>
      <th>sentiment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>This is the kind of movie that BEGS to be show...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bulletproof is quite clearly a disposable film...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A beautiful shopgirl in London is swept off he...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VERY dull, obvious, tedious Exorcist rip-off f...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Do we really need any more narcissistic garbag...</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
   <h4 class="admonition-title">Understanding the Data</h4>
   <ul>
      <li><mark style="background-color:#D7D0FF ">review</mark>: movie review in text format</li>
      <li><mark style="background-color:#D7D0FF ">sentiment</mark> : target value indicating whether the review has a positive or negative sentiment</li>
   </ul>
</div>

## __2. Create a Text Vectorization Model__

To create a text search model with the name <mark style="background-color:#E9D7FD ">movie_text_search_model</mark> using the <mark style="background-color:#FFEC92 ">movie_review_train</mark> dataset, run the following query.  
(Estimated time required for query execution: 2 min)


```python
%%thanosql
BUILD MODEL movie_text_search_model
USING SBERTEn
OPTIONS (
    text_col="review",
    overwrite=True
)
AS
SELECT *
FROM movie_review_train
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD">movie_text_search_model</mark>.</li>
        <li>"<strong>USING</strong>" specifies <mark style="background-color:#E9D7FD">SBERTEn</mark> as the base model.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create a model.
        <ul>
            <li>"text_col" : a column containing movie review data in the data table.</li>
            <li>"epochs" : number of times to train with the training dataset (int, DEFAULT : 1)</li>
            <li>"batch_size" : the size of dataset bundle utilized in a single cycle of training. (int, DEFAULT : 16)</li> 
            <li>"learning_rate" : the learning rate of the model (float, DEFAULT : 3e-5)</li> 
            <li>"train" : determines whether to use a pretrained model. If specified as False, the pretrained model is used. (True|False, DEFAULT : True)</li> 
            <li>"overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False)</li>
        </ul>
        </li>
    </ul>
</div>

To vectorize the `movie_review_test` texts run the following "__CONVERT USING__" query. The vectorized results are stored in a column named <mark style="background-color:#D7D0FF">movie_text_search_model_sberten</mark> in the `movie_review_test` table.


```python
%%thanosql
CONVERT USING movie_text_search_model
OPTIONS (
    text_col="review",
    table_name="movie_review_test",
    batch_size=32
    )
AS 
SELECT *
FROM movie_review_test
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
      <th>review</th>
      <th>sentiment</th>
      <th>movie_text_search_model_sberten</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>[-0.0035803898, -0.008503916700000001, 0.02663...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>[9.95681e-05, -0.0446751118, 0.0422799364, -0....</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>[0.0024927575, -0.0627047122, -0.0220359992, 0...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>[-0.0074431906, -0.0184612498, 0.0346845426, 0...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>[-0.0121047432, -0.08736537400000001, -0.01382...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>[-0.0081995381, -0.022497111900000002, 0.00271...</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>[-0.0258360021, -0.0056749573, 0.0006066648, 0...</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>[-0.0285293236, 0.0241474006, -0.0057142889, -...</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>[0.0242064726, -0.0032963406000000002, -0.0172...</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>[-4.81819e-05, 0.041250977700000004, -0.020035...</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>CONVERT USING</strong>" uses <code>movie_text_search_model</code> as an algorithm for text vectorizaion.</li>
        <li>"<strong>OPTIONS</strong>" specifies the options to be used for text vectorizaion.
        <ul>
            <li>"text_col" : a column containing movie review data in the data table.</li>
            <li>"table_name" : the table name to be stored in the ThanoSQL database.</li>
            <li>"batch_size" : the size of dataset bundle utilized in a single cycle of training.</li> 
        </ul>
        </li>
    </ul>
</div>

## __3. Search for Similar Texts__

This step uses the <mark style="background-color:#E9D7FD">movie_text_search_model</mark> text vectorization model and test table to search for similar texts.


```python
%%thanosql
SELECT review, sentiment, movie_text_search_model_sberten_similarity1 as score
FROM (
    SEARCH TEXT text="This movie was my favorite movie of all time"
    USING movie_text_search_model
    AS 
    SELECT * 
    FROM movie_review_test
    )
ORDER BY score DESC 
LIMIT 10
```

    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I have loved this movie since I saw it in the ...</td>
      <td>positive</td>
      <td>0.638331</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I always loved this film. The music,story and ...</td>
      <td>positive</td>
      <td>0.605845</td>
    </tr>
    <tr>
      <th>2</th>
      <td>First time I saw this great movie and Alyssa, ...</td>
      <td>positive</td>
      <td>0.602958</td>
    </tr>
    <tr>
      <th>3</th>
      <td>I loved this movie. It is a definite inspirati...</td>
      <td>positive</td>
      <td>0.599915</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This movie was the second movie I saw on the c...</td>
      <td>positive</td>
      <td>0.596825</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This film lingered and lingered at a small mov...</td>
      <td>positive</td>
      <td>0.588338</td>
    </tr>
    <tr>
      <th>6</th>
      <td>This movie was very good because it remember w...</td>
      <td>positive</td>
      <td>0.586384</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I saw this movie for the first time in 1988 wh...</td>
      <td>positive</td>
      <td>0.582169</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This is a delightful film. Elizabeth Taylor do...</td>
      <td>positive</td>
      <td>0.578149</td>
    </tr>
    <tr>
      <th>9</th>
      <td>What can I say?? This movie has it all...Roman...</td>
      <td>positive</td>
      <td>0.576739</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT review, sentiment, movie_text_search_model_sberten_similarity1 as score
FROM (
    SEARCH TEXT text="The movie was unsatisfactory"
    USING movie_text_search_model
    AS 
    SELECT * 
    FROM movie_review_test
    )
ORDER BY score DESC 
LIMIT 10
```

    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>While the dog was cute, the film was not. It w...</td>
      <td>negative</td>
      <td>0.707552</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A gave it a "2" instead of a "1" (awful) becau...</td>
      <td>negative</td>
      <td>0.704344</td>
    </tr>
    <tr>
      <th>2</th>
      <td>There was absolutely nothing in this film that...</td>
      <td>negative</td>
      <td>0.703998</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Just plain terrible. Nick and Michael are WAY ...</td>
      <td>negative</td>
      <td>0.703163</td>
    </tr>
    <tr>
      <th>4</th>
      <td>If this movie would have been in English, all ...</td>
      <td>negative</td>
      <td>0.702559</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Badly made. Dreadful acting and an ending that...</td>
      <td>negative</td>
      <td>0.689564</td>
    </tr>
    <tr>
      <th>6</th>
      <td>I knew this movie wasn't going to be amazing, ...</td>
      <td>negative</td>
      <td>0.685655</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Well, I remember when the studio sacked Schrad...</td>
      <td>negative</td>
      <td>0.675767</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This movie was pointless. I can't even call it...</td>
      <td>negative</td>
      <td>0.672833</td>
    </tr>
    <tr>
      <th>9</th>
      <td>4 out of 10. This film was neither funny as a ...</td>
      <td>negative</td>
      <td>0.669799</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [images|audio|videos|texts|keywords]</strong>" defines the image|audio|video|text|keyword data type to search for.</li>
        <li>"<strong>USING</strong>" defines the model used for the text vectorization.</li>
        <li>"<strong>AS</strong>" defines the embedding table to be used for the searches. In this example, <code>movie_review_test</code> table is used.</li>
    </ul>
</div>

## __4. Extract Keywords from Texts__

This step uses the <mark style="background-color:#E9D7FD">movie_text_search_model</mark> text vectorization model and test table to extract keywords from the texts.


```python
%%thanosql
SEARCH KEYWORD
USING movie_text_search_model
OPTIONS (
    text_col="review",
    ngram_range=[1, 3],
    use_stopwords=True
    )
AS 
SELECT * 
FROM movie_review_test
LIMIT 10 OFFSET 40
```

    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>movie_text_search_model_sberten</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Like an earlier commentor, I saw it in 1980 an...</td>
      <td>positive</td>
      <td>[-0.008661733000000001, -0.0488559045, 0.00187...</td>
      <td>{'keyword': ['the gripping story', 'dramatic m...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I'm a fan of Matthew Modine, but this film--wh...</td>
      <td>negative</td>
      <td>[-0.0112550212, 0.0041147466, -1.364e-07, -0.0...</td>
      <td>{'keyword': ['watched it', 'screenwriter and d...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Paul Lukas played a Russian intellectual makin...</td>
      <td>positive</td>
      <td>[0.0207437705, -0.050762143, -0.01628353630000...</td>
      <td>{'keyword': ['deadpan comic actor', 'name stan...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>to be honest, i didn't watch all of the origin...</td>
      <td>negative</td>
      <td>[0.0070642396, -0.0065534478, -0.0103744725, 0...</td>
      <td>{'keyword': ['other vampire movie', 'bad it wa...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Police Squad! (1982) was a funny show that end...</td>
      <td>positive</td>
      <td>[-0.0234945826, -0.040255721700000004, 0.00883...</td>
      <td>{'keyword': ['comedy hit airplane', 'police sq...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>I am still shuddering at the thought of EVER s...</td>
      <td>negative</td>
      <td>[-0.0264584627, -0.0002144384, 0.004705308, -0...</td>
      <td>{'keyword': ['better action movies', 'but the ...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Gregory Peck gives a brilliant performance in ...</td>
      <td>positive</td>
      <td>[0.0078235855, 0.0121560059, -0.0218212046, -0...</td>
      <td>{'keyword': ['a brilliant performance', 'this ...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I first flicked onto the LoG accidentally one ...</td>
      <td>positive</td>
      <td>[-0.0272676237, -0.0017859461, 0.0039674025, 0...</td>
      <td>{'keyword': ['watch it', 'the fast show', 'hum...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>I didn't know what to make of this film. I gue...</td>
      <td>positive</td>
      <td>[-0.0384476818, -0.0128671648, 0.013419711, -0...</td>
      <td>{'keyword': ['view the film', 'think to apprec...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>A family looking for some old roadside attract...</td>
      <td>negative</td>
      <td>[0.0140419025, -0.036462456000000004, -0.01798...</td>
      <td>{'keyword': ['poorly made horror', 'beloved fr...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT review, sentiment, keyword -> 'keyword' AS keywords, keyword -> 'score' AS score
FROM (
    SEARCH KEYWORD 
    USING movie_text_search_model
    OPTIONS (
        text_col="review",
        use_stopwords=True
        )
    AS 
    SELECT * 
    FROM movie_review_test
    LIMIT 10
)
```

    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>[film very haunting, best adaptations out, tal...</td>
      <td>[0.49860000000000004, 0.42610000000000003, 0.4...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>[9 11 hosted, the twin towers, television show...</td>
      <td>[0.5721, 0.5537000000000001, 0.389700000000000...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>[she meets jared, gritty rape scene, innocent ...</td>
      <td>[0.4808, 0.38970000000000005, 0.2985, 0.205500...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>[michael jackson film, music video of, liked t...</td>
      <td>[0.5556, 0.42210000000000003, 0.3995, 0.274400...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>[series the ghetto, brother jermaine brandon, ...</td>
      <td>[0.39940000000000003, 0.33690000000000003, 0.3...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Tipping the Velvet (2002) (TV) was directed by...</td>
      <td>positive</td>
      <td>[protagonist nan astley, directed by geoffrey,...</td>
      <td>[0.4555, 0.4006, 0.2677, 0.2137, 0.1925]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>The Stock Market Crash of 1929 and the Depress...</td>
      <td>positive</td>
      <td>[james cagney in, favorite busby berkeley, dor...</td>
      <td>[0.49870000000000003, 0.4056, 0.3929, 0.3773, ...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I want to clarify a few things. I am not famil...</td>
      <td>negative</td>
      <td>[an opportunistic movie, taiwan censorship and...</td>
      <td>[0.5604, 0.3019, 0.26940000000000003, 0.2106, ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This is a nice movie with good performances by...</td>
      <td>positive</td>
      <td>[spanish cinema, is very good, great directors...</td>
      <td>[0.5735, 0.48810000000000003, 0.42260000000000...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Once a month, I invite a few friends over for ...</td>
      <td>negative</td>
      <td>[retarded movie night, zombie i personally, jo...</td>
      <td>[0.545, 0.32230000000000003, 0.3074, 0.2487, 0...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql 
SELECT * FROM (
    SELECT review, sentiment, json_array_elements(keyword -> 'keyword') AS keywords, (json_array_elements(keyword -> 'score'))::text::float AS score
        FROM (
            SEARCH KEYWORD 
            USING movie_text_search_model
            OPTIONS (
                text_col="review",
                use_stopwords=True
                )
            AS 
            SELECT * 
            FROM movie_review_test
            LIMIT 10
        )
    ) 
WHERE score > 0.5
```

    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>9 11 hosted</td>
      <td>0.5721</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>the twin towers</td>
      <td>0.5537</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>michael jackson film</td>
      <td>0.5556</td>
    </tr>
    <tr>
      <th>3</th>
      <td>This is a nice movie with good performances by...</td>
      <td>positive</td>
      <td>spanish cinema</td>
      <td>0.5735</td>
    </tr>
    <tr>
      <th>4</th>
      <td>I want to clarify a few things. I am not famil...</td>
      <td>negative</td>
      <td>an opportunistic movie</td>
      <td>0.5604</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Once a month, I invite a few friends over for ...</td>
      <td>negative</td>
      <td>retarded movie night</td>
      <td>0.5450</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH KEYWORD [images|audio|videos|texts|keywords]</strong>" defines the image|audio|video|text|keyword data type to search for.</li>
        <li>"<strong>USING</strong>" defines the model used for the text vectorization.</li>
        <li>"<strong>OPTIONS</strong>" specifies the options to be used for the text vectorizaion.
            <ul>
                <li>"lang" : en, ko</li>
                <li>"text_col" :  a column containing movie review data in the data table</li>
                <li>"ngram_range" : minimum and maximum number of words for each keyword. ex) [1, 3]. In most situations, keywords are extracted according to the maximum number of words. (list[int, int], DEFAULT : [1, 2])</li>
                <li>"top_n" : number of keywords to be extracted, in order of highest similarity (int, DEFAULT : 5)</li>
                <li>"diversity" : variety of keywords to be extracted. The higher the value, the more diverse the keywords will be. 0 <= diversity <= 1 (float, DEFAULT : 0.5)</li>
                <li>"use_stopwords" : whether to exclude words that do not have a significant meaning (True|False, DEFAULT : True)</li>
                <li>"threshold" : minimum value of similarity value of keywords to be extracted. (float, DEFAULT : 0.0)</li>
            </ul>
        <li>"<strong>AS</strong>" defines the embedding table to be used for searches. In this example, <code>movie_review_test</code> table is used.</li>
    </ul>
</div>

## __5. Combine the Two Methods__


```python
%%thanosql
SEARCH KEYWORD
USING movie_text_search_model
OPTIONS (
    text_col="review",
    ngram_range=[1, 3],
    use_stopwords=True
    )
AS (
    SELECT review, sentiment, movie_text_search_model_sberten_similarity1 as score
    FROM (
        SEARCH TEXT text="The greatest movie of all time"
        USING movie_text_search_model
        AS 
        SELECT * 
        FROM movie_review_test
        )
    ORDER BY score DESC 
    LIMIT 10
)
```

    Searching...
    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>score</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>For those out there that like to think of them...</td>
      <td>positive</td>
      <td>0.574758</td>
      <td>{'keyword': ['well paced movie', 'who love fil...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>This is the best Chinese movie I have ever see...</td>
      <td>positive</td>
      <td>0.569135</td>
      <td>{'keyword': ['best chinese movie', 'beautiful ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>This is without a doubt the greatest film ever...</td>
      <td>positive</td>
      <td>0.568749</td>
      <td>{'keyword': ['greatest film ever', 'decipher t...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A labor of love. Each frame is picture perfect...</td>
      <td>positive</td>
      <td>0.555554</td>
      <td>{'keyword': ['recommend this film', 'the remar...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This is the kind of film for a snowy Sunday af...</td>
      <td>positive</td>
      <td>0.555060</td>
      <td>{'keyword': ['hours wonderful performances', '...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This film lingered and lingered at a small mov...</td>
      <td>positive</td>
      <td>0.554078</td>
      <td>{'keyword': ['warming film', 'it a comedy', 'c...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>This film immediately catches the eye, with th...</td>
      <td>positive</td>
      <td>0.549254</td>
      <td>{'keyword': ['film', 'compelling story', 'love...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>This is without a doubt the most poorly though...</td>
      <td>negative</td>
      <td>0.545353</td>
      <td>{'keyword': ['give this movie', 'the invention...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>The movie Angels of the Universe is a pure mas...</td>
      <td>positive</td>
      <td>0.541722</td>
      <td>{'keyword': ['best foreign film', 'movie angel...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>I was lured to see this movie by its starpower...</td>
      <td>negative</td>
      <td>0.534548</td>
      <td>{'keyword': ['modern thriller', 'the bad guys'...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT review, sentiment, keyword -> 'keyword' AS keywords, keyword -> 'score' AS score
FROM ( 
    SEARCH KEYWORD
    USING movie_text_search_model
    OPTIONS (
        text_col="review",
        ngram_range=[1, 3],
        use_stopwords=True
        )
    AS (
        SELECT review, sentiment, movie_text_search_model_sberten_similarity1 as score
        FROM (
            SEARCH TEXT text="Such a romatic movie"
            USING movie_text_search_model
            AS 
            SELECT * 
            FROM movie_review_test
            )
        ORDER BY score DESC 
        LIMIT 10
    )
)
```

    Searching...
    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>This is the kind of film for a snowy Sunday af...</td>
      <td>positive</td>
      <td>[hours wonderful performances, a family film, ...</td>
      <td>[0.5846, 0.5518000000000001, 0.414000000000000...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Burlinson and Thornton give an outstanding per...</td>
      <td>positive</td>
      <td>[the horse scenes, love this movie, are absolu...</td>
      <td>[0.6133000000000001, 0.5341, 0.429600000000000...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>"Crush" examines female friendship, for the mo...</td>
      <td>positive</td>
      <td>[crush examines female, film, emotional truth ...</td>
      <td>[0.48450000000000004, 0.4355, 0.3473, 0.296, 0...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>i would have to say that this is the first qua...</td>
      <td>positive</td>
      <td>[quality romantic comedy, movie was well, end ...</td>
      <td>[0.5969, 0.49910000000000004, 0.3899, 0.179800...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>"Casomai" is a masterful tale depicting the st...</td>
      <td>positive</td>
      <td>[best italian movies, masterful tale depicting...</td>
      <td>[0.6425000000000001, 0.5043, 0.323200000000000...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This film immediately catches the eye, with th...</td>
      <td>positive</td>
      <td>[film, compelling story, love and the, colours...</td>
      <td>[0.5419, 0.4838, 0.3158, 0.28350000000000003, ...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>A labor of love. Each frame is picture perfect...</td>
      <td>positive</td>
      <td>[recommend this film, the remarkable story, th...</td>
      <td>[0.636, 0.5346000000000001, 0.3505000000000000...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>A gentle story, hinting at fury, with a redemp...</td>
      <td>positive</td>
      <td>[well executed cinematographers, film score, t...</td>
      <td>[0.5819, 0.5658000000000001, 0.5315, 0.4884, 0...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Forget the recent dire American remake which s...</td>
      <td>positive</td>
      <td>[the intricate screenplay, deftly drawn romant...</td>
      <td>[0.5155000000000001, 0.5103, 0.440600000000000...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>There are not many movies around that have giv...</td>
      <td>positive</td>
      <td>[wonderful fantasy spectacle, bride stardust i...</td>
      <td>[0.5861000000000001, 0.36150000000000004, 0.28...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT review, sentiment, json_array_elements(keyword -> 'keyword') AS keywords, (json_array_elements(keyword -> 'score'))::text::float AS score
FROM (
    SEARCH KEYWORD
    USING movie_text_search_model
    OPTIONS (
        text_col="review",
        ngram_range=[1, 3],
        use_stopwords=True
        )
    AS (
        SELECT review, sentiment, movie_text_search_model_sberten_similarity1 as score
        FROM (
            SEARCH TEXT text="The best action movie"
            USING movie_text_search_model
            AS 
            SELECT * 
            FROM movie_review_test
            WHERE review LIKE '%%gun%%'
            )
        ORDER BY score DESC 
        LIMIT 10
    )
)
WHERE score > 0.45
```

    Searching...
    Searching...





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
      <th>review</th>
      <th>sentiment</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>decent films i</td>
      <td>0.5256</td>
    </tr>
    <tr>
      <th>1</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>good old days</td>
      <td>0.2753</td>
    </tr>
    <tr>
      <th>2</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>who will happily</td>
      <td>0.2280</td>
    </tr>
    <tr>
      <th>3</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>33 in dallas</td>
      <td>0.1801</td>
    </tr>
    <tr>
      <th>4</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>which betty white</td>
      <td>0.1790</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>western genre film</td>
      <td>0.5798</td>
    </tr>
    <tr>
      <th>6</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>movie i liked</td>
      <td>0.3852</td>
    </tr>
    <tr>
      <th>7</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>other surprising actors</td>
      <td>0.3839</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>involved quite amazing</td>
      <td>0.3069</td>
    </tr>
    <tr>
      <th>9</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>because the rifle</td>
      <td>0.2364</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [images|audio|videos|texts|keywords]</strong>" defines the image|audio|video|text|keyword data type to search for.</li>
        <li>"<strong>SEARCH KEYWORD [images|audio|videos|texts|keywords]</strong>" defines the image|audio|video|text|keyword data type to search for.</li>
        <li>"<strong>USING</strong>" defines the model used for text vectorization.</li>
        <li>"<strong>AS</strong>" defines the embedding table to be used for searches. In this example, <code>movie_review_test</code> table is used.</li>
    </ul>
</div>

## __6. In Conclusion__

In this tutorial, we performed text vectorization using `movie review data`, and similar text search and keyword extraction. As this is a beginner-level tutorial, we focused on the process rather than accuracy. The model's accuracy can be improved by adjusting various options, such as increasing the epoch or dataset size. Create your own model and provide competitive services by combining various unstructured data (image, audio, video, etc.) and structured data with ThanoSQL.
<br>
For the next step, explore the various "OPTIONS" and training methods of text vectorization models. If you want to learn more about building your own text model, proceed with the following tutorials.

* [How to Upload to ThanoSQL DB](https://docs.thanosql.ai/en/getting_started/data_upload/)
* [Creating an Intermediate Similar Text Search Model]

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your services, please feel free to contact us below😊</p>
    <p>For inquiries regarding building an text similarity search model: contact@smartmind.team</p>
</div>
