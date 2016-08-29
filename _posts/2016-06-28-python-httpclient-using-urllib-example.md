---
layout: post
title: Python - httpclient example using urllib
description: Python
modified: 2016-06-28
tags: [python, network]
comments: true
image:
  feature: abstract-11.png
---
Python HTTP client can be very easily built by urllib.

## Example

Here is the example.

```python
import urllib, urllib2, urlparse, json
import calendar, datetime, time, sys

def autheniticate(baseurl, userinfo, api_key):
    # POST Request manipulation
        ## URL : ~/user/authenticate
	url = urlparse.urljoin(baseurl, "user/authenticate")

        ## POST Payload
	payload = {"email":userinfo["email"], "password": userinfo["password"]}
	req = urllib2.Request(url, json.dumps(payload))

        ## HTTP header 
	req.add_header("X-API-KEY", api_key)

    # Send Request and receive the response
	response = urllib2.urlopen(req)
	response_data = response.read()

    # Convert the response into json format
	try: 
        js = json.loads(response_data)
	except: 
        print "Error in Authentication Response"; 
        exit()

    # Get elements from the json response
        ## "session_token" : "abcdefg"
        ## "user_id" : "hochul.shin@example.com" 
	sessioninfo = {}
	sessioninfo["token"] = js['session_token']
	sessioninfo["user_id"] = js['user_id']
	return sessioninfo

def getFutureUTC(m):
    # Current time + delta
	future = datetime.datetime.utcnow() + datetime.timedelta(minutes = m)
	return calendar.timegm(future.timetuple())

def createLiveEvent(baseurl, sessioninfo, api_key, payload):
    # POST Request manipulation
        ## URL : ~/user/[user_id]/live_event
	eventpath = "user/" + sessioninfo["user_id"] + "/live_event"
	url = urlparse.urljoin(baseurl, eventpath)

        ## POST Payload
	req = urllib2.Request(url, json.dumps(payload)) 

        ## HTTP header 
	req.add_header("X-API-KEY", api_key)
	req.add_header("X-SESSION-TOKEN", sessioninfo["token"])

    # Send Request and receive the response
	response = urllib2.urlopen(req)
	response_data = response.read()

    # Convert the response into json format
	try: 
        js = json.loads(response_data)
	except: 
        print "Error in CreateLiveEvent Response"; 
        exit()

    # Get the value of the live_event_id element
	return js['live_event_id']	

def getLiveEventInfo(baseurl, sessioninfo, api_key, event_id):
    # GET Request manipulation
        ## URL : ~/user/[user_id]/live_event
	eventpath = "user/" + sessioninfo["user_id"] + "/live_event"
	url = urlparse.urljoin(baseurl, eventpath)

        ## GET Request
	req = urllib2.Request(url) 

        ## HTTP header 
	req.add_header("X-API-KEY", api_key)
	req.add_header("X-SESSION-TOKEN", sessioninfo['token'])

    # Send Request and receive the response
	response = urllib2.urlopen(req)
	response_data = response.read()

    # Convert the response into json format
	try: 
        js = json.loads(response_data)
	except: 
        print "Error in GetLiveEvents Response"; 
        exit()

    # Get the value of the live_events element
        ## live_events : { "down": "http://example.com/down", "up": "http://example.com/up" }

	eventinfo = js['live_events']
	return eventinfo

def countdown(aftersec, interval):
    # Print count-down on screen
	for i in range(aftersec):
		if i % interval == 0: print "Streaming will start in", str(aftersec - i), "seconds."
		if aftersec - i > 10: sys.stdout.write('.'); sys.stdout.flush()
		else: print aftersec - i
		i += 1
		time.sleep(1)

baseurl = 'https://example.com/' 
api_key = "xxxyyyxxxyyyxxyy"
userinfo = {
	"email" : 'hochul.shin0example.com', 
	"password": 'hochul.shin0password',
}

sessioninfo = autheniticate(baseurl, userinfo, api_key)

title = raw_input("Enter title [Test]:") or "Test"
after_min = 1
start_time = getFutureUTC(after_min) 
payload = {"title":title, "start_time": start_time}

event_id = createLiveEvent(baseurl, sessioninfo, api_key, payload)
eventinfo = getLiveEventInfo(baseurl, sessioninfo, api_key, event_id)
print "Downstream URL:", eventinfo["down"]
print "Upstream URL:", eventinfo["up"]

countdown(after_min*60, 20) #in sec
print "Launch!"

```

