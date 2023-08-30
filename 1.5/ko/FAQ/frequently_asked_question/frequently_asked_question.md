---
title: FAQs
---
# __FAQs__

## **1. 버전 업데이트 후 튜토리얼 코드가 작동하지 않아요**
ThanoSQL 문법에 업데이트가 있을 시 발생할 수 있는 현상입니다. contact@smartmind.team로 해당 내용 관련하여 연락 주시면 따로 조치를 취해드리도록 하겠습니다.

## **2. ThanoSQL에서 대문자 사용 시 오류가 나요** 
ThanoSQL에서는 모델명/컬럼명을 소문자로 처리하고 있습니다. 대문자 모델명/컬럼명을 사용하셨다면 소문자를 이용하여 해당 모델/컬럼을 불러오시기 바랍니다.

## **3. COPY 구문 사용하여 csv 파일을 읽어올 때 한국어 오류가 나요**
ThanoSQL COPY 구문은 utf-8 encoding 형식만 지원합니다. 한국어가 들어있는 csv 파일의 경우, utf-8 encoding 후 COPY 구문을 사용하시기 바랍니다.

## **4. ThanoSQL에서 지원하는 비정형 데이터의 종류는 무엇인가요?**
ThanoSQL에서는  이미지, 오디오, 그리고 비디오 데이터에 대해 아래의 확장자를 지원합니다.

- Image:  "jpg", "png"  
- Audio: "wav", "flac", "mp3"
- Video: "mp4"

!!! notice ""
    반면 Text 데이터의 경우, 따로 특별한 확장자를 요구하지 않습니다. 

!!! warning ""
    Image의 경우, 언급된 2가지 이외의 확장자는 안정적으로 동작하지 않을 수도 있습니다.

## **5. 코드 작성 시 괄호 자동 완성은 어떻게 설정하는 건가요?**
`Menu > Settings > Auto Close Brackets`를 클릭하시면 해당 기능을 사용할 수 있습니다.

!!! tip ""
    따옴표에 대해서도 자동 완성이 적용됩니다.

## **6. LIKE 문법 사용하여 지정한 칼럼 값과 유사한 패턴을 갖는지 확인할 때 “%” 연산자가 정상 작동하지 않습니다.**
ThanoSQL에서는 "%"를 이스케이프문으로 받아들이기 때문에, "%%" 라고 작성해야합니다. 

## **7. Base64 인코딩된 데이터 프레임 컬럼을 디코딩하려면 어떻게 해야 하나요?**

ThanoSQL의 REST API를 사용할 때 "__CONVERT__"를 사용하여 생성된 컬럼은 base64를 사용하여 인코딩됩니다. 따라서 바이트를 변환된 컬럼으로 사용하려면 base64의 b64decode를 사용하여 디코딩해야 합니다.

```python
# 바이트 배열로 다시 디코딩
import base64
df['인코딩된 컬럼'] = df['인코딩된 컬럼'].apply(lambda x: base64.b64encode(x))

# 바이트 배열을 플로트 배열로 디코딩
import numpy as np 
df['바이트 배열 컬럼'] = df['바이트 배열 컬럼'].apply(lambda x: list(np.frombuffer(x, dtype="float32")))
```