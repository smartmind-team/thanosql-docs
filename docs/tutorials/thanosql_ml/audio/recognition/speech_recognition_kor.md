# 음성 인식

음성 인식이란 사람의 음성이 포함된 오디오가 주어졌을 때, 이를 텍스트로 받아 쓰는 작업을 말합니다.

### 의도 & 제한사항

- 오디오 파일은 `.wav` 또는 `.flac` 형식의 파일이어야 합니다.

- 오디오 파일 경로를 담은 열과, 텍스트를 담은 열이 데이터에 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 음성 인식 모델 빌드

### 데이터

모델을 빌드하기 전에, 사용할 데이터를 확인해보겠습니다. 여기서 사용할 데이터는 [librispeech](https://www.openslr.org/12) 데이터의 작은 부분입니다. 데이터는 오디오 파일의 경로를 담은 열과, 해당 오디오의 스크립트를 담은 열로 되어있습니다. SQL 문법으로 데이터를 확인할 수 있습니다.


```sql
%load_ext thanosql
```

```sql
%%thanosql
SELECT * FROM librispeech_train
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
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>/data/librispeech/095.wav</td>
      <td>i've decided to enlist in the army</td>
    </tr>
    <tr>
      <th>96</th>
      <td>/data/librispeech/096.wav</td>
      <td>i also offered to help your brother to escape ...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>/data/librispeech/097.wav</td>
      <td>well now said meekin with asperity i don't agr...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>/data/librispeech/098.wav</td>
      <td>little did i expect however the spectacle whic...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>/data/librispeech/099.wav</td>
      <td>i look at my watch it's a quarter to eleven</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 2 columns</p>
</div>


```sql
%%thanosql
SELECT * FROM librispeech_test
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
      <td>/data/librispeech/100.wav</td>
      <td>at that word deception spoken with such self c...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/librispeech/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/librispeech/102.wav</td>
      <td>there appeared to be an immediate association ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/librispeech/103.wav</td>
      <td>down below in the quiet narrow street measured...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/librispeech/104.wav</td>
      <td>he closed the door behind their backs with res...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>/data/librispeech/105.wav</td>
      <td>the third day it was reported that the yankees...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>/data/librispeech/106.wav</td>
      <td>it is lined with emerald algae and mosses and ...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/librispeech/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/librispeech/108.wav</td>
      <td>there were never before such times in old kent...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/librispeech/109.wav</td>
      <td>what that little cabin</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/librispeech/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
    </tr>
    <tr>
      <th>11</th>
      <td>/data/librispeech/111.wav</td>
      <td>i get the pillows comfortably arranged on the ...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>/data/librispeech/112.wav</td>
      <td>he suffered all the pangs of a mother and he k...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/librispeech/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/librispeech/114.wav</td>
      <td>at all events we shall get there some day</td>
    </tr>
    <tr>
      <th>15</th>
      <td>/data/librispeech/115.wav</td>
      <td>the saxons of kent and the southern kingdoms g...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>/data/librispeech/116.wav</td>
      <td>the prospect was as black as the window pane a...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>/data/librispeech/117.wav</td>
      <td>who lives here are the people mad</td>
    </tr>
    <tr>
      <th>18</th>
      <td>/data/librispeech/118.wav</td>
      <td>don't you see how many uses we have found for ...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>/data/librispeech/119.wav</td>
      <td>he certainly possesses talents beyond the rude...</td>
    </tr>
  </tbody>
</table>
</div>



이제, thanosql을 사용하여 음성 인식 모델을 빌드합니다.


```sql
%%thanosql

BUILD MODEL my_asr_model
USING Wav2Vec2En
OPTIONS (audio_col='audio', text_col='text', epochs=1, batch_size=8)
AS SELECT * FROM librispeech_train
```

<br>

## 예측

전 단계에서 만든 음성 인식 모델을 사용하여 오디오 음성 파일을 잘 인식하는지 확인해보겠습니다.


```sql
%%thanosql
PREDICT USING my_asr_model
AS SELECT * FROM librispeech_test
```




<div>
<table border="1" class="dataframe">
  <thead>```sql
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
      <td>AT THAT WORD DECEPTION SPOKEN WITH SUCH SELF C...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/librispeech/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
      <td>THAN WHY INDULGE IN PROPHETIC PHANTASIES</td>
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
      <td>IT IS LINED WITH EMERALD ALGI AND MOSSES AND S...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/librispeech/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
      <td>THEN WHEN YOUR MANAGER ADDED TWO MORE WEEKS I ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/librispeech/108.wav</td>
      <td>there were never before such times in old kent...</td>
      <td>NER WERE NEVER BEFORE SUCH TIMES IN OLD KENTUCKY</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/librispeech/109.wav</td>
      <td>what that little cabin</td>
      <td>WHAT THAT LITTLE CABIN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/librispeech/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
      <td>GO QUIETLY ALONE NO HARM WILL BEFALL YOU</td>
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
      <td>HE SUFFERED ALL THE PANGS OF A MOTHER AND HE K...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/librispeech/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
      <td>ACCUSTOMED AS I HAD BEEN TO THE STEAM FERRYBOA...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/librispeech/114.wav</td>
      <td>at all events we shall get there some day</td>
      <td>AT ALL EVENTS WE SHALL GET THERE SOME DAY</td>
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
      <td>WHO LIVES HERE ARE THE PEOPLE MAD</td>
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



예측 결과는 모두 대문자로 나오는 것을 볼 수 있습니다.

결과를 파이썬 변수로 받고싶다면, 다음처럼 `%%thanosql`이 아니라 `%thanosql`을 사용하면 됩니다.


```sql
result = %thanosql PREDICT USING my_asr_model AS SELECT * FROM librispeech_test
```


```sql
result
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
      <td>AT THAT WORD DECEPTION SPOKEN WITH SUCH SELF C...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/librispeech/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
      <td>THAN WHY INDULGE IN PROPHETIC PHANTASIES</td>
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
      <td>IT IS LINED WITH EMERALD ALGI AND MOSSES AND S...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/librispeech/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
      <td>THEN WHEN YOUR MANAGER ADDED TWO MORE WEEKS I ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/librispeech/108.wav</td>
      <td>there were never before such times in old kent...</td>
      <td>NER WERE NEVER BEFORE SUCH TIMES IN OLD KENTUCKY</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/librispeech/109.wav</td>
      <td>what that little cabin</td>
      <td>WHAT THAT LITTLE CABIN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/librispeech/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
      <td>GO QUIETLY ALONE NO HARM WILL BEFALL YOU</td>
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
      <td>HE SUFFERED ALL THE PANGS OF A MOTHER AND HE K...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/librispeech/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
      <td>ACCUSTOMED AS I HAD BEEN TO THE STEAM FERRYBOA...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/librispeech/114.wav</td>
      <td>at all events we shall get there some day</td>
      <td>AT ALL EVENTS WE SHALL GET THERE SOME DAY</td>
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
      <td>WHO LIVES HERE ARE THE PEOPLE MAD</td>
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
