# Tutorial Example
https://www.markdownguide.org/basic-syntax/

## Model Description
EfficientNet model is a convolutional neural network architecture and scaling method that uniformly scales all dimensions of depth/width/resolution using a compound coefficient. It is
pretrained on a large collection of images in a supervised fashion, namely ImageNet-21k, at a resolution of 224x224 pixels. The model was fine-tuned on ImageNet, a dataset comprising 1 million images and 1,000 classes, also at resolution 224x224.

<br>

## Intended uses & Limitation
This model can be implemented in many different fields: manufacturing, medical, agricultural, health care, etc. For example, in the medical field, we can build a classification model that can detect anomalies in tissues, including tumors, bone cracks or various types of cancer using this EfficientNet model. This model can then be used to ~~ in real time. This will reduce resources, time, and money.

(__“Wow! I want to use it!”__ moment _right_ here…) <br>
(“Wow! I *want* to use it!” moment **right here**…) <br>
(“Wow! I want to use it!” moment ***right here***…)

<br>

____
---
***

## How to use
In order to use this model, you must follow following steps:
> 1. Prepare your data
>> we have to have image and label columns
>2. Build your model
>>    what is this AutoML

1. list1
    1. list1-list1
    2. list1-list2
2. list2
    1. list2-list1


[![puppy](/home/smartmind/thanosql_env/thanosql-tech-doc/Tutorials/20150629_112932.jpg)]


``` python
import pandas as pd
```
``` sql
SELECT * FROM table_jy
```







Tutorials

Tutorial Guideline :

Model Description
	- 모델 설명 : 무엇을 하는/위한 모델인지 설명

Intended uses & Limitation
- 모델이 사용될 수 있는 분야/부분 설명
- 모델의 한계 설명 (limitations on size of dataset, format, etc)

     1번 2번을 통해서 “Wow! I want to use it!”이 나올 수 있게 작성…

How to use
Preparing Dataset
- Preprocessing training data
ThanoSQL Grammar
-           BUILD MODEL model_usr
 	USING model
     	OPTIONS (param=var) AS (Input Data)
(if exist) Using pre-trained model
- How to build my own model using pre-trained

(Optional) Evaluation
model 성능

(Optional) Use cases
- 예) Kaggle dataset을 사용한 모델링 (dataset 사용할시 license 꼭 확인 하기)
- 예) 모델이 적용된 (성공)사례
- 예) BigQuery ML Tutorial


Tutorial Requirements :

단락 Style / Text Size & Indentation
Follow rules on ThanoSQL Technical Documentation

Code Blocks
Python / ThanoSQL 코드 작성시, Code Blocks 사용
	Code Blocks 설치 방법 :
부가기능 -> 부가기능 설치 -> Code Blocks 설치 -> Theme:Ocean 사용
