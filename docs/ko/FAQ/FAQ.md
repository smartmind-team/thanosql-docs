# **FREQUENTLY ASKED QUESTIONS**

## **1. 버전 업데이트 후 튜토리얼 코드가 작동하지 않아요**
ThanoSQL 문법에 업데이트가 있을 시 발생할 수 있는 현상입니다. contact@smartmind.team로 해당 내용 관련하여 연락 주시면 따로 조치를 취해드리도록 하겠습니다.

## **2. ThanoSQL에서 대문자 사용시 오류가 나요** 
ThanoSQL에서는 모델명/컬럼명을 소문자로 처리하고 있습니다. 대문자 모델명/컬럼명을 사용하셨다면 소문자를 이용하여 해당 모델/컬럼을 불러오시기 바랍니다.

## **3. COPY 구문 사용하여 csv 파일 읽어올 때 한국어 오류 나요**
ThanoSQL COPY 구문은 utf-8 encoding 형식만 지원합니다. 한국어가 들어있는 csv 파일의 경우, utf-8 encoding 후 COPY 구문 사용하시기 바랍니다. 
