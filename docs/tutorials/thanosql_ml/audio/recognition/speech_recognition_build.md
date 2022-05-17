# 음성 인식

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
      <td>/data/000.wav</td>
      <td>i noticed how white and well shaped his own ha...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/001.wav</td>
      <td>the only conflicts that occurred on irish soil...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/002.wav</td>
      <td>inquired shaggy in the metal forest</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/003.wav</td>
      <td>my grandmother always spoke in a very loud ton...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/004.wav</td>
      <td>the poets of succeeding ages have dwelt much i...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>/data/095.wav</td>
      <td>i've decided to enlist in the army</td>
    </tr>
    <tr>
      <th>96</th>
      <td>/data/096.wav</td>
      <td>i also offered to help your brother to escape ...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>/data/097.wav</td>
      <td>well now said meekin with asperity i don't agr...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>/data/098.wav</td>
      <td>little did i expect however the spectacle whic...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>/data/099.wav</td>
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
      <td>/data/100.wav</td>
      <td>at that word deception spoken with such self c...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/102.wav</td>
      <td>there appeared to be an immediate association ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/103.wav</td>
      <td>down below in the quiet narrow street measured...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/104.wav</td>
      <td>he closed the door behind their backs with res...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>/data/105.wav</td>
      <td>the third day it was reported that the yankees...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>/data/106.wav</td>
      <td>it is lined with emerald algae and mosses and ...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/108.wav</td>
      <td>there were never before such times in old kent...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/109.wav</td>
      <td>what that little cabin</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
    </tr>
    <tr>
      <th>11</th>
      <td>/data/111.wav</td>
      <td>i get the pillows comfortably arranged on the ...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>/data/112.wav</td>
      <td>he suffered all the pangs of a mother and he k...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/114.wav</td>
      <td>at all events we shall get there some day</td>
    </tr>
    <tr>
      <th>15</th>
      <td>/data/115.wav</td>
      <td>the saxons of kent and the southern kingdoms g...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>/data/116.wav</td>
      <td>the prospect was as black as the window pane a...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>/data/117.wav</td>
      <td>who lives here are the people mad</td>
    </tr>
    <tr>
      <th>18</th>
      <td>/data/118.wav</td>
      <td>don't you see how many uses we have found for ...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>/data/119.wav</td>
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
