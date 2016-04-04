---
layout: post
title: python - urllib
description: python
modified: 2016-03-28
tags: [python]
comments: true
image:
  feature: algorithm.jpeg
---
urllib and urllib2 are great modules to manipulate http messages.
The following example is perfect to make you understand how them. 
This code is used for an assignment for [Coursera: Using Python to Access Web Data](https://www.coursera.org/learn/python-network-data/). For furhter information, check the [Coursera: Using Python to Access Web Data](https://www.coursera.org/learn/python-network-data/) out. 


### POST request with json body(request/response) and extra headers

#### example

```javascript
import urllib, urllib2
import json

url = 'https://dummy.com/api/user/profile'

# user input and default values for payload
email = raw_input("Enter your email address [yyy.zzz@gmail.com]:") or "yyy.zzz@gmail.com"
password = raw_input("Enter your password [xxxxxxxxx]:") or "xxxxxxxxx"
duration = int(raw_input("Enter duration[30]:") or 30)

import calendar
import datetime
def getFutureUTC(m):
	future = datetime.datetime.utcnow() + datetime.timedelta(minutes = m)
	return calendar.timegm(future.timetuple())
start_time = getFutureUTC(1) # in 1 minute

payload = {"email":email, "password": password, "duration" : duration, "start_time": start_time}

# json payload on request
req = urllib2.Request(url)

# for POST request
req.add_data(json.dumps(payload))

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

js = json.loads(response_data)
print json.dumps(js, indent = 4)
token = js['items'][0]['session_token']
print "session_token", token
```
