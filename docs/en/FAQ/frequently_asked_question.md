---
title: FAQs
---

# __FAQs__
## **1. Tutorial code stopped working after latest update.**

This can be occured when there is an update to ThanoSQL.  Please contact us via contact@smartmind.team if you have any futher questions.

## **2. Got an error when using capital letter in ThanoSQL**

ThanoSQL handles model/column names in lowercase letters. Please use only lowercase letters for model names or column names to call the appropriate model or column.

## **3. Got an encoding error when reading csv file with COPY statement**

The ThanoSQL's COPY clause supports only the utf-8 encoding format. For csv files that contain Korean, use the COPY clause after utf-8 encoding.

## **4. Types of unstructured data ThanoSQL could support**

ThanoSQL supports the following extensions for Image and Audio data:

- Image :  "jpg", "png"
- Audio : "wav", "flac", "mp3"
- Video : "mp4"

!!! notice ""
    ThanoSQL does not require any special extension for text data.

## **5. I was automatically logged out, but what happens to the query statement that was running?**

If you were logged out, you don't have to worry because the query statement you initiated keeps working in the back of the system.