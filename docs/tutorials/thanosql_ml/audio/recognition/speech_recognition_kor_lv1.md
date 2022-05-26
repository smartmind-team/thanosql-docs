# 음성 인식 모델 만들기 

## (ThanoSQL 무작정 따라하기) 오디오 파일을 받아쓰는 음성 인식 모델 만들기

**[이전 문서 - Auto-ML을 사용하여 자전거 수요 예측 회귀 모델 만들기](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/regression/automl_regression/)** <br>| **[다음 문서 - ]()**

### 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간(실행시간): 10분
- 사용언어: SQL(100%)
- 참고문서: [LibriSpeech 데이터 세트](http://www.openslr.org/12)

<br>

음성 인식이란 사람의 음성이 포함된 오디오가 주어졌을 때, 이를 텍스트로 받아 쓰는 작업을 말합니다.

### 의도 & 제한사항

- 오디오 파일은 `.wav` 또는 `.flac` 형식의 파일이어야 합니다.

- 오디오 파일 경로를 담은 열과, 텍스트를 담은 열이 데이터에 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 데이터 확인

음성 인식 모델을 만들기 전에, 사용할 데이터를 확인해보겠습니다. 여기서 사용할 데이터는 `librispeech` 데이터로, 영어 음성 파일과 해당 음성 파일의 스크립트로 구성된 데이터입니다. ThanoSQL DB에는 해당 데이터의 일부분이 훈련용 데이터와 테스트 데이터로 구분되어 저장되어 있습니다. 아래 SQL문을 통해 데이터를 확인할 수 있습니다.

```sql
%load_ext thanosql
```

```sql
%%thanosql
SELECT * FROM librispeech_train LIMIT 5
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>audio</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>/data/librispeech/000.wav</td>
      <td>i noticed how white and well shaped his own ha...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/librispeech/001.wav</td>
      <td>the only conflicts that occurred on irish soil...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/librispeech/002.wav</td>
      <td>inquired shaggy in the metal forest</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/librispeech/003.wav</td>
      <td>my grandmother always spoke in a very loud ton...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/librispeech/004.wav</td>
      <td>the poets of succeeding ages have dwelt much i...</td>
    </tr>
  </tbody>
</table>
</div>

> #### 데이터 이해하기
> 
> `audio` 행은 음성 파일의 경로를 담고 있으며, `text`행은 해당 음성 파일의 스크립트를 담고 있습니다.

<br>

## 음성 인식 모델 빌드

이제, thanosql을 사용하여 음성 인식 모델을 빌드합니다.

```sql
%%thanosql
BUILD MODEL my_speech_recognition_model
USING Wav2Vec2En
OPTIONS (audio_col='audio', text_col='text', epochs=1, batch_size=4)
AS SELECT * FROM librispeech_train
```

    <Response [200]>

> #### 쿼리 세부 정보
> 
> BUILD MODEL 구문을 사용하여 `my_speech_recognition_model` 모델을 만듭니다. USING 구문을 통해 기반 모델로 `Wav2Vec2En`를 사용할 것을 명시합니다.
> 
> OPTIONS를 통해 빌드에 사용할 옵션을 지정합니다. `audio_col`은 학습에 사용할 오디오 경로를 담은 행의 이름이며, `text_col`은 해당 오디오의 스크립트 정보를 담은 행입니다. `epochs`를 통해 몇 번의 에포크로 학습할 지를 지정하고, `batch_size`를 통해 한번에 데이터를 몇 개씩 읽을지 정합니다. 여기서는 빠르게 학습하기 위해 epochs를 1로 지정했습니다.

<br>

## 결과 예측

빌드 완료된 모델을 사용하여, 오디오 파일의 스크립트를 읽어보겠습니다.

```sql
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (audio_col='audio')
AS SELECT * FROM librispeech_test
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>audio</th>
      <th>text</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>/data/librispeech/100.wav</td>
      <td>at that word deception spoken with such self c...</td>
      <td>AT THAT WORDDECEPTIONSPOKEN WITH SUCH SELF CON...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/librispeech/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
      <td>THAN WHYINDULGE IN PROPHETIC PHANTASIES</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/librispeech/102.wav</td>
      <td>there appeared to be an immediate association ...</td>
      <td>THERE APPEARED TO BE AN IMMEDIATE ASSOCIATION ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/librispeech/103.wav</td>
      <td>down below in the quiet narrow street measured...</td>
      <td>DOWN BELOW IN THE QUIET NARROW STREET MEASURED...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/librispeech/104.wav</td>
      <td>he closed the door behind their backs with res...</td>
      <td>HE CLOSED THE DOOR BEHIND THEIR BACKS WITH RES...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>/data/librispeech/105.wav</td>
      <td>the third day it was reported that the yankees...</td>
      <td>THE THIRD DAY IT WAS REPORTED THAT THE YANKEES...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>/data/librispeech/106.wav</td>
      <td>it is lined with emerald algae and mosses and ...</td>
      <td>IT IS LINED WITH EMERALD ALGI AND MOSSESAND SH...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/librispeech/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
      <td>THEN WHEN YOUR MANAGER ADDED TWO MORE WEEKSI W...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/librispeech/108.wav</td>
      <td>there were never before such times in old kent...</td>
      <td>NHERE WERE NEVER BEFORE SUCH TIMES IN OLD KENT...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/librispeech/109.wav</td>
      <td>what that little cabin</td>
      <td>WHATTHAT LITTLE CABIN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/librispeech/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
      <td>GO QUIETLYALONENO HARM WILL BEFALL YOU</td>
    </tr>
    <tr>
      <th>11</th>
      <td>/data/librispeech/111.wav</td>
      <td>i get the pillows comfortably arranged on the ...</td>
      <td>I GET THE PILLOWS COMFORTABLY ARRANGED ON THE ...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>/data/librispeech/112.wav</td>
      <td>he suffered all the pangs of a mother and he k...</td>
      <td>HE SUFFERED ALL THE PANGS OF A MOTHERAND HE KN...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/librispeech/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
      <td>ACCUSTOMEDAS I HAD BEN TO THE STEAM FERRYBOATS...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/librispeech/114.wav</td>
      <td>at all events we shall get there some day</td>
      <td>AT ALL EVENTSWE SHALL GET THERESOMEDAY</td>
    </tr>
    <tr>
      <th>15</th>
      <td>/data/librispeech/115.wav</td>
      <td>the saxons of kent and the southern kingdoms g...</td>
      <td>THE SAXONS OF KENT AND THE SOUTHERN KINGDOMS G...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>/data/librispeech/116.wav</td>
      <td>the prospect was as black as the window pane a...</td>
      <td>THE PROSPECT WAS AS BLACK AS THE WINDOWPANE AG...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>/data/librispeech/117.wav</td>
      <td>who lives here are the people mad</td>
      <td>WHO LIVES HERE ARE THE PEOPLEMAD</td>
    </tr>
    <tr>
      <th>18</th>
      <td>/data/librispeech/118.wav</td>
      <td>don't you see how many uses we have found for ...</td>
      <td>DON'T YOU SEE HOW MANY USES WE HAVE FOUND FOR ...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>/data/librispeech/119.wav</td>
      <td>he certainly possesses talents beyond the rude...</td>
      <td>HE CERTAINLY POSSESSES TALENTS BEYOND THE RUDE...</td>
    </tr>
  </tbody>
</table>
</div>

> #### 쿼리 세부 정보
> 
> PREDICT USING 쿼리를 통해 이전 단계에서 만든 `my_speech_recognition_model` 모델을 사용할 것이라 지정해줍니다.
> 
> OPTIONS를 통해 예측에 사용할 옵션을 지정합니다. `audio_col`은 예측에 사용할 오디오의 경로를 담은 행의 이름입니다.
