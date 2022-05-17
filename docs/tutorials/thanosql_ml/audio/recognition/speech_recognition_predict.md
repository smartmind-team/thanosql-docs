# 음성 인식

## 예측

전 단계에서 만든 음성 인식 모델을 사용하여 오디오 음성 파일을 잘 인식하는지 확인해보겠습니다.


```sql
%load_ext thanosql
```


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
      <td>/data/100.wav</td>
      <td>at that word deception spoken with such self c...</td>
      <td>AT THAT WORD DECEPTION SPOKEN WITH SUCH SELF C...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
      <td>THAN WHY INDULGE IN PROPHETIC PHANTASIES</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/102.wav</td>
      <td>there appeared to be an immediate association ...</td>
      <td>THERE APPEARED TO BE AN IMMEDIATE ASSOCIATION ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/103.wav</td>
      <td>down below in the quiet narrow street measured...</td>
      <td>DOWN BELOW IN THE QUIET NARROW STREET MEASURED...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/104.wav</td>
      <td>he closed the door behind their backs with res...</td>
      <td>HE CLOSED THE DOOR BEHIND THEIR BACKS WITH RES...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>/data/105.wav</td>
      <td>the third day it was reported that the yankees...</td>
      <td>THE THIRD DAY IT WAS REPORTED THAT THE YANKEES...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>/data/106.wav</td>
      <td>it is lined with emerald algae and mosses and ...</td>
      <td>IT IS LINED WITH EMERALD ALGI AND MOSSES AND S...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
      <td>THEN WHEN YOUR MANAGER ADDED TWO MORE WEEKS I ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/108.wav</td>
      <td>there were never before such times in old kent...</td>
      <td>NER WERE NEVER BEFORE SUCH TIMES IN OLD KENTUCKY</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/109.wav</td>
      <td>what that little cabin</td>
      <td>WHAT THAT LITTLE CABIN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
      <td>GO QUIETLY ALONE NO HARM WILL BEFALL YOU</td>
    </tr>
    <tr>
      <th>11</th>
      <td>/data/111.wav</td>
      <td>i get the pillows comfortably arranged on the ...</td>
      <td>I GET THE PILLOWS COMFORTABLY ARRANGED ON THE ...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>/data/112.wav</td>
      <td>he suffered all the pangs of a mother and he k...</td>
      <td>HE SUFFERED ALL THE PANGS OF A MOTHER AND HE K...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
      <td>ACCUSTOMED AS I HAD BEEN TO THE STEAM FERRYBOA...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/114.wav</td>
      <td>at all events we shall get there some day</td>
      <td>AT ALL EVENTS WE SHALL GET THERE SOME DAY</td>
    </tr>
    <tr>
      <th>15</th>
      <td>/data/115.wav</td>
      <td>the saxons of kent and the southern kingdoms g...</td>
      <td>THE SAXONS OF KENT AND THE SOUTHERN KINGDOMS G...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>/data/116.wav</td>
      <td>the prospect was as black as the window pane a...</td>
      <td>THE PROSPECT WAS AS BLACK AS THE WINDOWPANE AG...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>/data/117.wav</td>
      <td>who lives here are the people mad</td>
      <td>WHO LIVES HERE ARE THE PEOPLE MAD</td>
    </tr>
    <tr>
      <th>18</th>
      <td>/data/118.wav</td>
      <td>don't you see how many uses we have found for ...</td>
      <td>DON'T YOU SEE HOW MANY USES WE HAVE FOUND FOR ...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>/data/119.wav</td>
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
      <td>/data/100.wav</td>
      <td>at that word deception spoken with such self c...</td>
      <td>AT THAT WORD DECEPTION SPOKEN WITH SUCH SELF C...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/101.wav</td>
      <td>then why indulge in prophetic phantasies</td>
      <td>THAN WHY INDULGE IN PROPHETIC PHANTASIES</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/102.wav</td>
      <td>there appeared to be an immediate association ...</td>
      <td>THERE APPEARED TO BE AN IMMEDIATE ASSOCIATION ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/103.wav</td>
      <td>down below in the quiet narrow street measured...</td>
      <td>DOWN BELOW IN THE QUIET NARROW STREET MEASURED...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/104.wav</td>
      <td>he closed the door behind their backs with res...</td>
      <td>HE CLOSED THE DOOR BEHIND THEIR BACKS WITH RES...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>/data/105.wav</td>
      <td>the third day it was reported that the yankees...</td>
      <td>THE THIRD DAY IT WAS REPORTED THAT THE YANKEES...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>/data/106.wav</td>
      <td>it is lined with emerald algae and mosses and ...</td>
      <td>IT IS LINED WITH EMERALD ALGI AND MOSSES AND S...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/data/107.wav</td>
      <td>then when your manager added two more weeks i ...</td>
      <td>THEN WHEN YOUR MANAGER ADDED TWO MORE WEEKS I ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>/data/108.wav</td>
      <td>there were never before such times in old kent...</td>
      <td>NER WERE NEVER BEFORE SUCH TIMES IN OLD KENTUCKY</td>
    </tr>
    <tr>
      <th>9</th>
      <td>/data/109.wav</td>
      <td>what that little cabin</td>
      <td>WHAT THAT LITTLE CABIN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>/data/110.wav</td>
      <td>go quietly alone no harm will befall you</td>
      <td>GO QUIETLY ALONE NO HARM WILL BEFALL YOU</td>
    </tr>
    <tr>
      <th>11</th>
      <td>/data/111.wav</td>
      <td>i get the pillows comfortably arranged on the ...</td>
      <td>I GET THE PILLOWS COMFORTABLY ARRANGED ON THE ...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>/data/112.wav</td>
      <td>he suffered all the pangs of a mother and he k...</td>
      <td>HE SUFFERED ALL THE PANGS OF A MOTHER AND HE K...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>/data/113.wav</td>
      <td>accustomed as i had been to the steam ferry bo...</td>
      <td>ACCUSTOMED AS I HAD BEEN TO THE STEAM FERRYBOA...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>/data/114.wav</td>
      <td>at all events we shall get there some day</td>
      <td>AT ALL EVENTS WE SHALL GET THERE SOME DAY</td>
    </tr>
    <tr>
      <th>15</th>
      <td>/data/115.wav</td>
      <td>the saxons of kent and the southern kingdoms g...</td>
      <td>THE SAXONS OF KENT AND THE SOUTHERN KINGDOMS G...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>/data/116.wav</td>
      <td>the prospect was as black as the window pane a...</td>
      <td>THE PROSPECT WAS AS BLACK AS THE WINDOWPANE AG...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>/data/117.wav</td>
      <td>who lives here are the people mad</td>
      <td>WHO LIVES HERE ARE THE PEOPLE MAD</td>
    </tr>
    <tr>
      <th>18</th>
      <td>/data/118.wav</td>
      <td>don't you see how many uses we have found for ...</td>
      <td>DON'T YOU SEE HOW MANY USES WE HAVE FOUND FOR ...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>/data/119.wav</td>
      <td>he certainly possesses talents beyond the rude...</td>
      <td>HE CERTAINLY POSSESSES TALENTS BEYOND THE RUDE...</td>
    </tr>
  </tbody>
</table>
</div>
