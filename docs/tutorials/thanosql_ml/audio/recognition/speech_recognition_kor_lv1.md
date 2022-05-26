# 음성 인식 모델 만들기 

## (ThanoSQL 무작정 따라하기) 오디오 파일을 받아쓰는 음성 인식 모델 만들기

**[이전 문서 - Auto-ML을 사용하여 자전거 수요 예측 회귀 모델 만들기](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/regression/automl_regression/)** <br>| **[다음 문서 - ]()**


## 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간: 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일 : 
- 참고문서: [LibriSpeech 데이터 세트](http://www.openslr.org/12), [wav2vec 2.0: A Framework for Self-Supervised Learning of Speech Representations](https://arxiv.org/abs/2006.11477)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
> __음성 인식 이해하기__ <br>
> 컴퓨터 음성 인식 또는 음성-텍스트 변환(Speech-to-Text)이라고도 부르는 음성 인식은 프로그램이 사람의 음성을 텍스트 형식으로 처리할 수 있도록 해주는 기능입니다. 일반적으로 음성 인식(Voice Recognition)과 혼동 될 수 있는데, 음성 인식은 개별 사용자의 목소리를 식별하는 데만 집중합니다. 최근 자동차, 의료 분야, 인공지능 스피커나 스마트폰을 이용한 일상 생활까지 광범위한 분야에서 활용되고 있습니다. 최근 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5) 알고리즘을 활용한 음성 인식 솔루션은 문법, 구문, 구조, 오디오와 음성 신호의 구성을 통합하여 음성을 이해하고 처리합니다. <br>

오늘날 수많은 산업 분야에서 음성 인식 기술의 다양한 응용 기술이 활용되고 있습니다. 기업과 소비자들은 음성 인식 모델을 통해 시간을 절감할 수 있고 때로는 생명까지 구하기도 합니다. 인공지능을 활용한 가상 안내원/비서/모델의 목소리를 만들기도 하고 특정 연예인이나 유명인의 목소리를 흉내내어 정해진 지문을 목소리로 변환하기도 합니다. 음성 인식 기술의 발전은 단순 여행용 자동 통역에서 난이도가 높은 비즈니스 회의 통역까지 확대되고 있습니다. 음성 인식 기술을 활용한 서비스는 사용자의 손발을 자유롭게 사용할 수 있고, 언어의 장벽을 허물어 줄 수 있기에 다양한 응용이 가능합니다.

__아래는 ThanoSQL 음성 인식 모델의 활용 및 예시입니다.__
- 일상 생활에서 인공지능 스피커나 스마트폰에서 사용되는 [가상 비서](https://ko.wikipedia.org/wiki/%EA%B0%80%EC%83%81_%EB%B9%84%EC%84%9C)(빅스비, 시리, 알렉스, 코타나 등) 기능 등을 통해 음성 검색을 수행하거나 다양한 어플리케이션, 사물인터넷 기기 등에 연결하고 제어 명령을 내릴 수 있습니다. 음성 인식을 통해 목소리를 텍스트로 변환하고 화자의 의도, 감정, 토픽 분석 등 다양한 자연어 처리 기법들을 적용할 수 있습니다. 자동차부터 생활로봇 등 음성 인식 모델의 활용 분야는 계속 확장되고 있습니다. 
- 음성 인식 모델을 이용하면 전화 상담 데이터를 텍스트로 변환할 수 있고 이를 이용해서 사용자의 감정 분석이나 상담 트렌드 등을 분석할 수 있습니다. 초보 상담사에게는 사용자의 문의에 대한 답변이 될 수 있는 후보 답변이나 과거의 유사 사례, 필요한 정보 등을 빠르게 검색하고 제공할 수 있습니다. 음성으로 저장되어 있는 상담 데이터는 상담 종료 후 주제에 따라 적절하게 분류되어 저장이 가능하고 감정 분석 등을 통해 고객의 만족도 등을 간접적으로 측정할 수 있습니다. 또한 음성 인식 모델을 이용하면 키보드로 작성하는 메모보다 빠르게 작성이 가능하고, 긴 음성파일에서도 빠르게 특정 키워드를 검색 할 수 있습니다. 특히, 화상 회의나 동영상 강의/콘텐츠가 많아지면서 음성 인식 모델의 도움을 받으면 더욱 정확한 범주 분류나 추천 모델 등에 활용이 가능합니다. 

> __본 튜토리얼에서는__ <br>
> Librispeech [Panayotov et al. 2015]는 현존 음성인식 연구에 있어서 가장 널리 사용되는 대규모 영어 음성 데이터 중 하나로 사용자 참여형 오디오북 프로젝트인 [LibriVox project](https://librivox.org/)의 결과물입니다. 16kHz로 샘플링된 약 1,000시간 분량의 녹음된 오디오북 데이터를 가공하여 만들었습니다. 튜토리얼을 위한 대상 테이블은 미리 업로드 된 음성 파일의 경로와 스크립트로 구성되어 있습니다. 본 튜토리얼은 음성 파일을 텍스트로 변환하는 것을 목표로 하고 있습니다. 오디오 형식의 파일을 DB에 직접 저장하는 방식은 [비정형 데이터 변환]() 튜토리얼을 참조하세요.  <br>

> __튜토리얼의 의도 및 제한사항__
> - ThanoSQL에서 현재 지원하는 오디오 파일의 형식은 '.wav', '.flac' 입니다. 
> - 오디오 파일 경로를 나타내는 컬럼(Column)과, 목표값(Target)에 해당하는 텍스트를 나타내는 컬럼이 테이블에 존재해야 합니다.
> - 해당 음성 인식 모델의 베이스 모델(`Wav2Vec2En`)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.
<br>


## 1. 데이터 세트 확인

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는 `librispeech_train` 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.

```python
%%thanosql
SELECT * 
FROM librispeech_train 
LIMIT 5
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

> #### __데이터 이해하기__ <br>
> 
> - `audio`: 음성 파일의 위치 경로
> - `text`: 해당 음성의 목표값(Target, 스크립트) <br>

> (향후 내용 추가 필요) PRINT를 이용한 이미지 결과 샘플 출력 <br>


## 2. 음성 인식 모델 만들기

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
