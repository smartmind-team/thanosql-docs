# 간단하게 ThanoSQL REST API 사용하기 

## 토큰 발행 
ThanoSQL의 REST API를 사용하기 위해서는 자신만의 토큰을 발행해야 합니다. 토큰을 발행하기 위해서는 https://thanosql.ai를 방문하여 밑의 절차에 따라 간단하게 받아보실수 있습니다. 토큰은 발급 받을때 한번만 볼수 있기 때문에 바로 다른 안전한 곳에 저장 하기 바랍니다. API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토크은 더 이상 사용 할수 없는 점 유의 하시기 바랍니다. 

screen shots </br>
screen shots </br>
screen shots </br>
screen shots </br>

You must add the token to the header when sending the API Query as an Authorization  header
밑에 보이는 것 처럼 
```python
## REST Header

header = {"Authorization" : "Bearer {YOURTOKENSTRING}"}
```
## Sending API call 
Sending API calls are easy. It is basically the same as using the ThanoSQL from ThanoSQL platform. You just need to add the token as a header and make query calls as you would in your python environment. The queries must be sent as strings. You must make the API call to https://server.thanosql.ai/api/v1/ 

## Python Example to QUERY structured data
```python
## Using python requests library
import requests

query_url="/query/image"
base_url="http://server.thanosql.ai/api/v1"
token=f"{YOURTOKENSTRING}"
header={"Authorization" : "Bearer " + token}
query="SELECT * FROM my_table"

r = requests.post(base_url + query_url, headers=header, data=json.dumps(query))
r.raise_for_status()

return_json = r.json()
```

## Unstructured Data
Three types of unstructured data are supported for ThanoSQL's REST API (for now). Images, Audio, and Video. The return response will be the respective file types or will be a zip file if there are multiple return files (10 images, 5 audios, 5 videos).

Python Example to QUERY unstructured data

```python
## Assuming that the image file is saved in the same file path as the current working directory
import requests

query_url="/query/image"
base_url="http://server.thanosql.ai/api/v1"
token=f"{YOURTOKENSTRING}"
header={"Authorization" : "Bearer " + token}
query="INSERT IMAGE INTO image_table"

with open('example.jpeg', 'rb') as f:
  r = requests.post(base_url + query_url, files={'example.jpg': f}, headers=header, data=query)

with open('/results/example.jpeg', 'wb') as f:
  f.write(r.content)
```