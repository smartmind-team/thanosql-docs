# Accessing THANOSQL over REST API

## Token
You must get an assigned API Token from the https://thanosql.ai website. This token is only shown once. You can always get another one, but it will render the previous token invalid.

You must add the token to the header when sending the API Query as an Authorization  header



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