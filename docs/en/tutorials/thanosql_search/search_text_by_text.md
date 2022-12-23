---
title: Search Text by Text
---

# __Search Text by Text__

- Tutorial Difficulty: ★★☆☆☆
- 7 min read 
- Languages: [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location: tutorial_en/thanosql_search/search_text_by_text.ipynb   
- References: [(Kaggle) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data), [Word Embeddings: LEXICAL SEMANTICS Encoding](https://pytorch.org/tutorials/beginner/nlp/word_embeddings_tutorial.html)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Text Vectorization</h4>
    <p>Computers cannot directly interpret human language(natural language). Therefore, a process for converting natural language into numerical data that can be recognized by computers is required. In the field of natural language processing, embedding refers to the result of converting human natural language into a vectorized format, which is a form that can be understood by machines.</p>
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
            <li>"overwrite": determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, default: False) </li>
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
           <li>"overwrite": determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. Check Dataset__

To create a movie review text search model, we use the <mark style="background-color:#FFEC92 ">movie_review_train</mark> table from the ThanoSQL workspace database. To check the table's contents, run the following query.


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
      <li><mark style="background-color:#D7D0FF ">sentiment</mark>: target value indicating whether the review has a positive or negative sentiment</li>
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

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD">movie_text_search_model</mark>.</li>
        <li>"<strong>USING</strong>" specifies <mark style="background-color:#E9D7FD">SBERTEn</mark> as the base model.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create a model.
        <ul>
            <li>"text_col": a column containing movie review data in the data table. (default: "text")</li>
            <li>"epochs": number of times to train with the training dataset (int, default: 1)</li>
            <li>"batch_size": the size of dataset bundle utilized in a single cycle of training. (int, default: 16)</li> 
            <li>"learning_rate": the learning rate of the model (float, default: 3e-5)</li> 
            <li>"train": determines whether to use a pretrained model. If specified as False, the pretrained model is used. (True|False, default: True)</li> 
            <li>"overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, default: False)</li>
        </ul>
        </li>
    </ul>
</div>

To vectorize the `movie_review_test` texts run the following "__CONVERT USING__" query. The vectorized results are stored in a user-defined column (default: <mark style="background-color:#D7D0FF">convert_result</mark>) in the `movie_review_test` table.


```python
%%thanosql
CONVERT USING movie_text_search_model
OPTIONS (
    text_col="review",
    table_name="movie_review_test",
    batch_size=32,
    column_name="convert_result"
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
      <th>convert_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>[0.014037576, 0.027578924, 0.02471304, -0.0176...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>[0.027382538, -0.021728337, 0.03685805, -0.044...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>[0.014439536, -0.02376758, -0.0065942346, 0.03...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>[0.012731184, 0.020806227, 0.0311054, -0.00222...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>[-0.0044349744, -0.06204289, 0.017439988, -0.0...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>Ocean's 12 starts off on annoying and gets wor...</td>
      <td>negative</td>
      <td>[0.02004037, 0.015577145, 0.0042112377, -0.051...</td>
    </tr>
    <tr>
      <th>996</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>[0.016599433, 0.008860707, 0.007274589, 0.0302...</td>
    </tr>
    <tr>
      <th>997</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>[-0.0038948876, 0.033606753, 0.02673663, 0.000...</td>
    </tr>
    <tr>
      <th>998</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>[0.00040617603, 0.03672079, -0.010704152, -0.0...</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>[0.05034484, 0.026520131, -0.010891634, -0.018...</td>
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
            <li>"text_col": a column containing movie review data in the data table. (default: "text")</li>
            <li>"table_name": the table name to be stored in the ThanoSQL workspace database.</li>
            <li>"batch_size": the size of dataset bundle utilized in a single cycle of training. (int, default: 16)</li>
            <li>"column_name": defines the column name that contains the vectorized results. (default: "convert_result")</li>
        </ul>
        </li>
    </ul>
</div>

## __3. Search for Similar Texts__

This step uses the <mark style="background-color:#E9D7FD">movie_text_search_model</mark> text vectorization model and test table to search for similar texts.


```python
%%thanosql
SELECT review, sentiment, score
FROM (
    SEARCH TEXT text="This movie was my favorite movie of all time"
    USING movie_text_search_model
    OPTIONS (
        emb_col="convert_result",
        column_name="score"
        )
    AS 
    SELECT * 
    FROM movie_review_test
    )
ORDER BY score DESC 
LIMIT 10
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
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I have loved this movie since I saw it in the ...</td>
      <td>positive</td>
      <td>0.741026</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I always loved this film. The music,story and ...</td>
      <td>positive</td>
      <td>0.735022</td>
    </tr>
    <tr>
      <th>2</th>
      <td>I saw this movie for the first time in 1988 wh...</td>
      <td>positive</td>
      <td>0.734215</td>
    </tr>
    <tr>
      <th>3</th>
      <td>This movie is Jackie's best. I still cant get ...</td>
      <td>positive</td>
      <td>0.731211</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This is a delightful film. Elizabeth Taylor do...</td>
      <td>positive</td>
      <td>0.728850</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This movie was the second movie I saw on the c...</td>
      <td>positive</td>
      <td>0.716220</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Very heart warming and uplifting movie. Outsta...</td>
      <td>positive</td>
      <td>0.706558</td>
    </tr>
    <tr>
      <th>7</th>
      <td>My husband and I both loved this film. At firs...</td>
      <td>positive</td>
      <td>0.702175</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This has to be one of the best movies to come ...</td>
      <td>positive</td>
      <td>0.700377</td>
    </tr>
    <tr>
      <th>9</th>
      <td>What can I say?? This movie has it all...Roman...</td>
      <td>positive</td>
      <td>0.697864</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT review, sentiment, score
FROM (
    SEARCH TEXT text="The movie was unsatisfactory"
    USING movie_text_search_model
    OPTIONS (
        emb_col="convert_result",
        column_name="score"
        )
    AS 
    SELECT * 
    FROM movie_review_test
    )
ORDER BY score DESC 
LIMIT 10
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
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Just plain terrible. Nick and Michael are WAY ...</td>
      <td>negative</td>
      <td>0.745355</td>
    </tr>
    <tr>
      <th>1</th>
      <td>There was absolutely nothing in this film that...</td>
      <td>negative</td>
      <td>0.745163</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A gave it a "2" instead of a "1" (awful) becau...</td>
      <td>negative</td>
      <td>0.744775</td>
    </tr>
    <tr>
      <th>3</th>
      <td>We brought this film as a joke for a friend, a...</td>
      <td>negative</td>
      <td>0.742644</td>
    </tr>
    <tr>
      <th>4</th>
      <td>I knew this movie wasn't going to be amazing, ...</td>
      <td>negative</td>
      <td>0.733149</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This movie looked fun on the cover and I hones...</td>
      <td>negative</td>
      <td>0.730956</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Badly made. Dreadful acting and an ending that...</td>
      <td>negative</td>
      <td>0.728068</td>
    </tr>
    <tr>
      <th>7</th>
      <td>This movie was so unrelentingly bad, I could h...</td>
      <td>negative</td>
      <td>0.724352</td>
    </tr>
    <tr>
      <th>8</th>
      <td>The premise of this movie was decent enough, b...</td>
      <td>negative</td>
      <td>0.722977</td>
    </tr>
    <tr>
      <th>9</th>
      <td>While the dog was cute, the film was not. It w...</td>
      <td>negative</td>
      <td>0.722429</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [image|audio|video|text|keyword]</strong>" defines the image|audio|video|text|keyword data type to search for.</li>
        <li>"<strong>USING</strong>" defines the model used for the text vectorization.</li>
        <li>"<strong>OPTIONS</strong>" specifies the options to be used for text searching.
        <ul>
                <li>"emb_col": the column that contains the vectorized results.</li>
                <li>"column_name": defines the name of the column that contains the search results. (default: "search_result")</li>
        </ul>
        </li>
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
      <th>convert_result</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Like an earlier commentor, I saw it in 1980 an...</td>
      <td>positive</td>
      <td>[0.019816624, -0.033710007, -0.01097258, -0.03...</td>
      <td>{'keyword': ['story splendid acting', 'writers...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I'm a fan of Matthew Modine, but this film--wh...</td>
      <td>negative</td>
      <td>[0.004325681, 0.015616861, -0.0028261256, -0.0...</td>
      <td>{'keyword': ['film', 'i watched it', 'all this...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Paul Lukas played a Russian intellectual makin...</td>
      <td>positive</td>
      <td>[0.034945264, -0.03700541, 0.004724759, -0.047...</td>
      <td>{'keyword': ['comedies of preston', 'celebrity...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>to be honest, i didn't watch all of the origin...</td>
      <td>negative</td>
      <td>[0.03015102, 0.013378834, 0.002104831, 0.00672...</td>
      <td>{'keyword': ['other vampire movie', 'horror ha...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Police Squad! (1982) was a funny show that end...</td>
      <td>positive</td>
      <td>[0.00814611, -0.014099172, 0.018553793, -0.013...</td>
      <td>{'keyword': ['a funny show', 'comedian in this...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>I am still shuddering at the thought of EVER s...</td>
      <td>negative</td>
      <td>[0.0071278927, 0.030981911, 0.006835275, -0.02...</td>
      <td>{'keyword': ['seeing this movie', 'but the plo...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Gregory Peck gives a brilliant performance in ...</td>
      <td>positive</td>
      <td>[0.034372132, 0.0354431, -0.02025241, -0.06389...</td>
      <td>{'keyword': ['a brilliant performance', 'the f...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I first flicked onto the LoG accidentally one ...</td>
      <td>positive</td>
      <td>[-5.251513e-05, 0.03718008, 0.008339626, -0.00...</td>
      <td>{'keyword': ['t watch it', 'an amazing plot', ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>I didn't know what to make of this film. I gue...</td>
      <td>positive</td>
      <td>[-0.004017977, 0.018181568, 0.0027857828, -0.0...</td>
      <td>{'keyword': ['view the film', 'think i will', ...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>A family looking for some old roadside attract...</td>
      <td>negative</td>
      <td>[0.0325733, -0.010194275, -0.012354068, -0.038...</td>
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
      <td>[best adaptations, the movie, talented filmake...</td>
      <td>[0.5384, 0.5241, 0.4566, 0.4392, 0.3636]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>[9/11 hosted, television show, 2001, robert de...</td>
      <td>[0.6425, 0.4782, 0.4376, 0.3801, 0.306]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>[rape scene, she is, little hesitant, become s...</td>
      <td>[0.4252, 0.266, 0.2627, 0.2198, 0.1661]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>[music film, song bad, i liked, 6/10 segment, ...</td>
      <td>[0.5701, 0.5011, 0.4273, 0.362, 0.3005]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>[supernatural clich, brother jermaine, the ghe...</td>
      <td>[0.4682, 0.4022, 0.3658, 0.3223, 0.2118]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Tipping the Velvet (2002) (TV) was directed by...</td>
      <td>positive</td>
      <td>[protagonist nan, excellent actor, rachael sti...</td>
      <td>[0.4539, 0.3763, 0.3458, 0.3283, 0.2516]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>The Stock Market Crash of 1929 and the Depress...</td>
      <td>positive</td>
      <td>[james cagney, between musicals, screened his,...</td>
      <td>[0.504, 0.4535, 0.3151, 0.3124, 0.2208]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I want to clarify a few things. I am not famil...</td>
      <td>negative</td>
      <td>[art movies, undeserved commercial, is boring,...</td>
      <td>[0.5926, 0.3547, 0.3486, 0.3375, 0.2642]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This is a nice movie with good performances by...</td>
      <td>positive</td>
      <td>[better movie, spanish cinema, is very, leonar...</td>
      <td>[0.5518, 0.5473, 0.3112, 0.216, 0.0852]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Once a month, I invite a few friends over for ...</td>
      <td>negative</td>
      <td>[retarded movie, standard zombie, friends howe...</td>
      <td>[0.5442, 0.2274, 0.2009, 0.1933, 0.1527]</td>
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
      <td>best adaptations</td>
      <td>0.5384</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>the movie</td>
      <td>0.5241</td>
    </tr>
    <tr>
      <th>2</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>9/11 hosted</td>
      <td>0.6425</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>music film</td>
      <td>0.5701</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>song bad</td>
      <td>0.5011</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This is a nice movie with good performances by...</td>
      <td>positive</td>
      <td>better movie</td>
      <td>0.5518</td>
    </tr>
    <tr>
      <th>6</th>
      <td>The Stock Market Crash of 1929 and the Depress...</td>
      <td>positive</td>
      <td>james cagney</td>
      <td>0.5040</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I want to clarify a few things. I am not famil...</td>
      <td>negative</td>
      <td>art movies</td>
      <td>0.5926</td>
    </tr>
    <tr>
      <th>8</th>
      <td>This is a nice movie with good performances by...</td>
      <td>positive</td>
      <td>spanish cinema</td>
      <td>0.5473</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Once a month, I invite a few friends over for ...</td>
      <td>negative</td>
      <td>retarded movie</td>
      <td>0.5442</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH KEYWORD</strong>" uses the movie_text_search_model as an algorithm to search keywords.</li>
        <li>"<strong>USING</strong>" defines the model used for the text vectorization.</li>
        <li>"<strong>OPTIONS</strong>" specifies the options to be used for the text vectorizaion.
            <ul>
                <li>"lang": en, ko</li>
                <li>"text_col": a column containing movie review data in the data table</li>
                <li>"ngram_range": minimum and maximum number of words for each keyword. ex) [1, 3]. In most situations, keywords are extracted according to the maximum number of words. (list[int, int], default: [1, 2])</li>
                <li>"top_n": number of keywords to be extracted, in order of highest similarity (int, default: 5)</li>
                <li>"diversity": variety of keywords to be extracted. The higher the value, the more diverse the keywords will be. 0 <= diversity <= 1 (float, default: 0.5)</li>
                <li>"use_stopwords": whether to exclude words that do not have a significant meaning (True|False, default: True)</li>
                <li>"threshold": minimum value of similarity value of keywords to be extracted. (float, default: 0.0)</li>
            </ul>
            </li>
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
    SELECT review, sentiment, score
    FROM (
        SEARCH TEXT text="The greatest movie of all time"
        USING movie_text_search_model
        OPTIONS (
            emb_col="convert_result",
            column_name="score"
            )
        AS 
        SELECT * 
        FROM movie_review_test
        )
    ORDER BY score DESC 
    LIMIT 10
    )
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
      <th>score</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>This is without a doubt the most poorly though...</td>
      <td>negative</td>
      <td>0.656462</td>
      <td>{'keyword': ['movie in history', 'bad as this'...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>This is a delightful film. Elizabeth Taylor do...</td>
      <td>positive</td>
      <td>0.655172</td>
      <td>{'keyword': ['recommend this film', 'delightfu...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The beginning of this film is a little clunky ...</td>
      <td>positive</td>
      <td>0.654622</td>
      <td>{'keyword': ['thriller', 'film is', 'fine perf...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>First ever viewing: July 21, 2008 Very impress...</td>
      <td>positive</td>
      <td>0.653558</td>
      <td>{'keyword': ['best adapted screenplay', 'rarel...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This is without a doubt the greatest film ever...</td>
      <td>positive</td>
      <td>0.647122</td>
      <td>{'keyword': ['greatest film ever', 'improvised...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This is the best Chinese movie I have ever see...</td>
      <td>positive</td>
      <td>0.645870</td>
      <td>{'keyword': ['best chinese movie', 'movie defi...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>This film may seem dated today, but remember t...</td>
      <td>positive</td>
      <td>0.643857</td>
      <td>{'keyword': ['made in 1974', 'heavy satiric hu...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>This movie is Jackie's best. I still cant get ...</td>
      <td>positive</td>
      <td>0.640296</td>
      <td>{'keyword': ['movie is jackie', 'great story',...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Devil Dog sets your heart racing. It's brillia...</td>
      <td>negative</td>
      <td>0.634332</td>
      <td>{'keyword': ['brilliantly paced', 'the ending'...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>This movie was the second movie I saw on the c...</td>
      <td>positive</td>
      <td>0.631678</td>
      <td>{'keyword': ['movie great monsters', 'truly ro...</td>
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
        SELECT review, sentiment, score
        FROM (
            SEARCH TEXT text="Such a romatic movie"
            USING movie_text_search_model
            OPTIONS (
                emb_col="convert_result",
                column_name="score"
                )
            AS 
            SELECT * 
            FROM movie_review_test
            )
        ORDER BY score DESC 
        LIMIT 10
        )
    )
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
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>i would have to say that this is the first qua...</td>
      <td>positive</td>
      <td>[quality romantic comedy, movie was well, stil...</td>
      <td>[0.6767, 0.6377, 0.2868, 0.2609, 0.221]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Burlinson and Thornton give an outstanding per...</td>
      <td>positive</td>
      <td>[the horse scenes, love this movie, are absolu...</td>
      <td>[0.6556, 0.6132, 0.4957, 0.41, 0.1869]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Very heart warming and uplifting movie. Outsta...</td>
      <td>positive</td>
      <td>[uplifting movie outstanding, saw this movie, ...</td>
      <td>[0.6771, 0.5455, 0.4628, 0.2198, 0.1636]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Modern, original, romantic story. Very good ac...</td>
      <td>positive</td>
      <td>[very good acting, original romantic story, go...</td>
      <td>[0.5996, 0.5457, 0.4167, 0.2933, 0.2207]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>I came across this movie back in the mid eight...</td>
      <td>positive</td>
      <td>[great christmas romance, film he, stanwyck is...</td>
      <td>[0.6426, 0.4323, 0.4279, 0.3628, 0.3341]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Forget the recent dire American remake which s...</td>
      <td>positive</td>
      <td>[the intricate screenplay, deftly drawn romant...</td>
      <td>[0.6248, 0.5623, 0.4376, 0.3847, 0.3049]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>My husband and I both loved this film. At firs...</td>
      <td>positive</td>
      <td>[loved this film, wonderfully loveable and, hu...</td>
      <td>[0.6392, 0.3914, 0.242, 0.1692, 0.1441]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>This is a delightful film. Elizabeth Taylor do...</td>
      <td>positive</td>
      <td>[recommend this film, delightful film elizabet...</td>
      <td>[0.7081, 0.6414, 0.4529, 0.3554, 0.2257]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>I have loved this movie since I saw it in the ...</td>
      <td>positive</td>
      <td>[loved this movie, movie it is, dating loves i...</td>
      <td>[0.5947, 0.498, 0.2693, 0.2514, 0.1677]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Dani(Reese Witherspoon) has always been very c...</td>
      <td>positive</td>
      <td>[her first movie, heartbreaking story about, a...</td>
      <td>[0.5191, 0.4982, 0.4643, 0.3913, 0.223]</td>
    </tr>
  </tbody>
</table>
</div>




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
    SELECT review, sentiment, score
    FROM (
        SEARCH TEXT text="The best action movie"
        USING movie_text_search_model
        OPTIONS (
            emb_col="convert_result",
            column_name="score"
            )
        AS 
        SELECT * 
        FROM movie_review_test
        WHERE review LIKE '%%gun%%'
        )
    ORDER BY score DESC 
    LIMIT 10
    )
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
      <th>score</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>What was there about 1939 that helped produce ...</td>
      <td>positive</td>
      <td>0.581589</td>
      <td>{'keyword': ['excellent hollywood films', 'lon...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>0.571282</td>
      <td>{'keyword': ['very entertaining movie', 'no cr...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>0.541819</td>
      <td>{'keyword': ['truly good movies', 'before holl...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Countless Historical &amp; cultural mistakes 0/10 ...</td>
      <td>negative</td>
      <td>0.539601</td>
      <td>{'keyword': ['top 250 movies', 'movie 3 jewish...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Oh, dear! This has to be one of the worst film...</td>
      <td>negative</td>
      <td>0.516846</td>
      <td>{'keyword': ['the worst films', 'unbelievably ...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This is a very memorable spaghetti western. It...</td>
      <td>positive</td>
      <td>0.503518</td>
      <td>{'keyword': ['poorly choreographed and', 'movi...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>"In the world of old-school kung fu movies, wh...</td>
      <td>positive</td>
      <td>0.486575</td>
      <td>{'keyword': ['extraordinary kung fu', 'his lat...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>This was probably the worst movie ever, seriou...</td>
      <td>negative</td>
      <td>0.484984</td>
      <td>{'keyword': ['worst movie ever', 'cheesy porn ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>I first saw this as a child living in East Lon...</td>
      <td>positive</td>
      <td>0.480955</td>
      <td>{'keyword': ['history in cinema', 'space trave...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>I was very surprised how bad this movie was. N...</td>
      <td>negative</td>
      <td>0.456414</td>
      <td>{'keyword': ['kung fu movies', 'movie is worse...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT * 
FROM (
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
            SELECT review, sentiment, score
            FROM (
                SEARCH TEXT text="The best action movie"
                USING movie_text_search_model
                OPTIONS (
                    emb_col="convert_result",
                    column_name="score"
                    )
                AS 
                SELECT * 
                FROM movie_review_test
                WHERE review LIKE '%%gun%%'
                )
            ORDER BY score DESC 
            LIMIT 10
            )
        )
    )
WHERE score > 0.3
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
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>What was there about 1939 that helped produce ...</td>
      <td>positive</td>
      <td>excellent hollywood films</td>
      <td>0.6745</td>
    </tr>
    <tr>
      <th>1</th>
      <td>What was there about 1939 that helped produce ...</td>
      <td>positive</td>
      <td>long forgotten screwball</td>
      <td>0.4139</td>
    </tr>
    <tr>
      <th>2</th>
      <td>What was there about 1939 that helped produce ...</td>
      <td>positive</td>
      <td>the wedding</td>
      <td>0.3555</td>
    </tr>
    <tr>
      <th>3</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>very entertaining movie</td>
      <td>0.6288</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>no criticism really</td>
      <td>0.4036</td>
    </tr>
    <tr>
      <th>5</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>actors rock hudson</td>
      <td>0.3810</td>
    </tr>
    <tr>
      <th>6</th>
      <td>This is quite an unusual and unique little wes...</td>
      <td>positive</td>
      <td>tough gunslinger seems</td>
      <td>0.3562</td>
    </tr>
    <tr>
      <th>7</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>truly good movies</td>
      <td>0.5596</td>
    </tr>
    <tr>
      <th>8</th>
      <td>As a veteran screen writing instructor at Rich...</td>
      <td>positive</td>
      <td>before hollywood started</td>
      <td>0.4226</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Countless Historical &amp; cultural mistakes 0/10 ...</td>
      <td>negative</td>
      <td>top 250 movies</td>
      <td>0.5378</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Countless Historical &amp; cultural mistakes 0/10 ...</td>
      <td>negative</td>
      <td>movie 3 jewish</td>
      <td>0.5259</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Countless Historical &amp; cultural mistakes 0/10 ...</td>
      <td>negative</td>
      <td>brilliant detective was</td>
      <td>0.4577</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Countless Historical &amp; cultural mistakes 0/10 ...</td>
      <td>negative</td>
      <td>funny 4 hitler</td>
      <td>0.4401</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Countless Historical &amp; cultural mistakes 0/10 ...</td>
      <td>negative</td>
      <td>it was painful</td>
      <td>0.3387</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Oh, dear! This has to be one of the worst film...</td>
      <td>negative</td>
      <td>the worst films</td>
      <td>0.5977</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Oh, dear! This has to be one of the worst film...</td>
      <td>negative</td>
      <td>unbelievably repetitive</td>
      <td>0.4885</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Oh, dear! This has to be one of the worst film...</td>
      <td>negative</td>
      <td>excellent terminator the</td>
      <td>0.4302</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Oh, dear! This has to be one of the worst film...</td>
      <td>negative</td>
      <td>special effects are</td>
      <td>0.4212</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Oh, dear! This has to be one of the worst film...</td>
      <td>negative</td>
      <td>scene seems to</td>
      <td>0.3605</td>
    </tr>
    <tr>
      <th>19</th>
      <td>This is a very memorable spaghetti western. It...</td>
      <td>positive</td>
      <td>poorly choreographed and</td>
      <td>0.5744</td>
    </tr>
    <tr>
      <th>20</th>
      <td>This is a very memorable spaghetti western. It...</td>
      <td>positive</td>
      <td>movie it is</td>
      <td>0.5060</td>
    </tr>
    <tr>
      <th>21</th>
      <td>This was probably the worst movie ever, seriou...</td>
      <td>negative</td>
      <td>watch it i</td>
      <td>0.4588</td>
    </tr>
    <tr>
      <th>22</th>
      <td>This is a very memorable spaghetti western. It...</td>
      <td>positive</td>
      <td>villainess in this</td>
      <td>0.3890</td>
    </tr>
    <tr>
      <th>23</th>
      <td>This is a very memorable spaghetti western. It...</td>
      <td>positive</td>
      <td>the brawl really</td>
      <td>0.3725</td>
    </tr>
    <tr>
      <th>24</th>
      <td>"In the world of old-school kung fu movies, wh...</td>
      <td>positive</td>
      <td>extraordinary kung fu</td>
      <td>0.6527</td>
    </tr>
    <tr>
      <th>25</th>
      <td>"In the world of old-school kung fu movies, wh...</td>
      <td>positive</td>
      <td>his later movies</td>
      <td>0.5525</td>
    </tr>
    <tr>
      <th>26</th>
      <td>"In the world of old-school kung fu movies, wh...</td>
      <td>positive</td>
      <td>is arguably chang</td>
      <td>0.3733</td>
    </tr>
    <tr>
      <th>27</th>
      <td>"In the world of old-school kung fu movies, wh...</td>
      <td>positive</td>
      <td>golden harvest was</td>
      <td>0.3291</td>
    </tr>
    <tr>
      <th>28</th>
      <td>This was probably the worst movie ever, seriou...</td>
      <td>negative</td>
      <td>worst movie ever</td>
      <td>0.6463</td>
    </tr>
    <tr>
      <th>29</th>
      <td>This was probably the worst movie ever, seriou...</td>
      <td>negative</td>
      <td>cheesy porn movie</td>
      <td>0.6393</td>
    </tr>
    <tr>
      <th>30</th>
      <td>This was probably the worst movie ever, seriou...</td>
      <td>negative</td>
      <td>this a 1/10</td>
      <td>0.4393</td>
    </tr>
    <tr>
      <th>31</th>
      <td>I first saw this as a child living in East Lon...</td>
      <td>positive</td>
      <td>history in cinema</td>
      <td>0.6151</td>
    </tr>
    <tr>
      <th>32</th>
      <td>I first saw this as a child living in East Lon...</td>
      <td>positive</td>
      <td>space travel was</td>
      <td>0.4543</td>
    </tr>
    <tr>
      <th>33</th>
      <td>I first saw this as a child living in East Lon...</td>
      <td>positive</td>
      <td>war mans inhumanity</td>
      <td>0.4233</td>
    </tr>
    <tr>
      <th>34</th>
      <td>I first saw this as a child living in East Lon...</td>
      <td>positive</td>
      <td>well crafted work</td>
      <td>0.3742</td>
    </tr>
    <tr>
      <th>35</th>
      <td>I was very surprised how bad this movie was. N...</td>
      <td>negative</td>
      <td>kung fu movies</td>
      <td>0.5871</td>
    </tr>
    <tr>
      <th>36</th>
      <td>I was very surprised how bad this movie was. N...</td>
      <td>negative</td>
      <td>movie is worse</td>
      <td>0.5534</td>
    </tr>
    <tr>
      <th>37</th>
      <td>I was very surprised how bad this movie was. N...</td>
      <td>negative</td>
      <td>characters are ridiculous</td>
      <td>0.4985</td>
    </tr>
    <tr>
      <th>38</th>
      <td>I was very surprised how bad this movie was. N...</td>
      <td>negative</td>
      <td>go watch hero</td>
      <td>0.4469</td>
    </tr>
    <tr>
      <th>39</th>
      <td>I was very surprised how bad this movie was. N...</td>
      <td>negative</td>
      <td>virtually no plot</td>
      <td>0.4436</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [image|audio|video|text|keyword]</strong>" defines the image|audio|video|text|keyword data type to search for.</li>
        <li>"<strong>SEARCH KEYWORD</strong>" uses the movie_text_search_model as an algorithm to search keywords.</li>
        <li>"<strong>USING</strong>" defines the model used for text vectorization.</li>
        <li>"<strong>AS</strong>" defines the embedding table to be used for searches. In this example, <code>movie_review_test</code> table is used.</li>
    </ul>
</div>

## __6. In Conclusion__

In this tutorial, we performed text vectorization using `movie review data`, and similar text search and keyword extraction. As this is a beginner-level tutorial, we focused on the process rather than accuracy. The model's accuracy can be improved by adjusting various options, such as increasing the epoch or dataset size. Create your own model and provide competitive services by combining various unstructured data (image, audio, video, etc.) and structured data with ThanoSQL.

* [How to Upload My Data to the ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/data_upload/)
* [How to Create a Table Using My Data](https://docs.thanosql.ai/en/how-to_guides/ThanoSQL_query/COPY_SYNTAX/)
* [How to Upload My Model to the ThanoSQL Workspace](https://docs.thanosql.ai/en/how-to_guides/ThanoSQL_query/UPLOAD_SYNTAX/)

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your services, please feel free to contact us below😊</p>
    <p>For inquiries regarding building an text similarity search model: contact@smartmind.team</p>
</div>
