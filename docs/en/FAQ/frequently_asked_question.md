---
title: FAQs
---

# __FAQs__
## **1. Tutorial code stopped working after the latest update.**

This might happen when there is an update to the ThanoSQL syntax. If you have any further questions about this issue, please contact us at contact@smartmind.team and we will look into it as soon as possible. 

## **2. I got an error when using capital letters in ThanoSQL.**

ThanoSQL handles model/column names in lowercase letters. Please use only lowercase letters for model names or column names to call the appropriate model or column.

## **3. When I used the COPY statement to read a csv file, I received an encoding error.**

The COPY clause in ThanoSQL only supports the utf-8 encoding format. For csv files containing Korean, use the COPY clause after the appropriate utf-8 encoding.

## **4. What types of unstructured data does ThanoSQL support?**

ThanoSQL supports the following extensions for image, audio, and video data:

- Image:  "jpg", "png"
- Audio: "wav", "flac", "mp3"
- Video: "mp4"

!!! notice ""
    ThanoSQL does not require any special extensions for text data.

!!! warning ""
    For image, extensions other than the two mentioned may not work reliably.
## **5. How do I auto close brackets?**

You can turn on the feature by clicking on `Menu > Settings > Auto Close Brackets`.

!!! tip ""
    This also applies for quotation marks.

## **6. When using a LIKE statement, the “%” operator does not work.**

Since ThanoSQL uses “%” as an escape character, you need to use “%%” instead.

## **7. How do I decode base64 encoded DataFrame columns?**

When using ThanoSQL's REST API, columns created using "__CONVERT__" are encoded using base64. Therefore, to use it as a column containing bytes, it must be decoded using base64's b64decode.

```python
# to decode back to array an of bytes
import base64
df['column name with encoded values'] = df['column name with encoded values'].apply(lambda x: base64.b64encode(x))

# to decode array of bytes to array of floats 
import numpy as np 
df['column name with array of bytes'] = df['column name with array of bytes'].apply(lambda x: list(np.frombuffer(x, dtype="float32")))
```