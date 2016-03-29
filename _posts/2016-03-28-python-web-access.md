
### POST request with json body(request/response) and extra headers

#### example

```javascript
import urllib, urllib2
import json
import re

url = 'https://dummy.com/api/user/profile'
payload = {"email":"yyyy.zzzz@gmail.com", "password":"xxxxxxxx"}

# json payload on request
req = urllib2.Request(url, json.dumps(payload))

# extra headers
req.add_header("Content-Type","application/json")
req.add_header("X-API-KEY","56ad2e79edd1fc001d7fabasdfasdfieasxc.asdfasdasdfasdfk")

response = urllib2.urlopen(req)

print "----------- HEAD --------------"
response_headers = response.headers.headers
for r in response_headers:
	print r.strip()

print "----------- BODY ----------------"
response_data = response.read()
# json payload on response
robj = json.loads(response_data)
for r in robj:
	print r, ":", robj[r]
```
