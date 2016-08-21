---
layout: post
title: Python - json
description: Python
modified: 2016-03-28
tags: [python]
comments: true
image:
  feature: algorithm.jpeg
---
The python provides very simple and strong json module. However using the module is little bit confusing in spite of the intuitiveness.
The methods below are little bit complicated:
- json.dumps
- json.dump
- json.loads
- json.load

### json.loads

json.loads() returns an object from a string. The string must be json string. 
Let's take a look at the following example.

```javascript
import json

jstr = '''{"x" : 1, "y": "yes", "z": [3, 4]}'''

jobj = json.loads(jstr)

print jobj  ''' {u'y': u'yes', u'x': 1, u'z': [3, 4]}'''
print jobj['x'] ''' 1 '''
print jobj['y'] ''' "yes" '''
for i in jobj['z']:
    print i   ''' 3, 4 '''
```

### json.dumps

json.dumps() returns a string from an json object. Let's look at the example below.

```javascript
import json

jobj = {
    'x': 1, 
    'y': 'yes', 
    'z': [3, 4]
}
jstr = json.dumps(jobj)
print jstr ''' {"y": "yes", "x": 1, "z": [3, 4]} '''
```

### json.load

json.load is a kind of read function from file. json.load() returns an object from a file handler.

```javascript
import json

''' 
The content of the example.json file is
  {"x" : 1, "y": "yes", "z": [3, 4]}
'''

with open('example.json') as f:
  jstr = json.load(f) ''' instead of read() '''
  print jstr ''' {u'y': u'yes', u'x': 1, u'z': [3, 4]}'''
```

#### json.dump

json.dump() is a kind of write function to file. json.dump() takes two arguments. First is a json object and second is a file handler to write.
The example is below:

```javascript
import json

jobj = {
    'x': 1, 
    'y': 'yes', 
    'z': [3, 4]
}
f = open("example.json", 'w')
json.dump(f, jobj)

'''
The content of the example.json should be 
 {"y": "yes", "x": 1, "z": [3, 4]}
'''
```

### Simple example

The following is a tiny example used on [Coursera lecture : Using Python to Access Web Data](https://www.coursera.org/learn/python-network-data). 

```javascript
import urllib
import json

serviceurl = 'http://maps.googleapis.com/maps/api/geocode/json?'

while True:
    address = raw_input("Enter location:")
    if len(address) < 1: break

    url = serviceurl + urllib.urlencode({'sensor':'false', 'address': address})
    print 'Retrieving', url
    uh = urllib.urlopen(url)
    data = uh.read()

    print 'Retrieved', len(data), 'characters'

    try:
        js = json.loads(str(data))
    except: 
        js = None
    if 'status' not in data or js['status'] != 'OK':
        print 'Failure to Retrieve'
        print data
        continue

    print json.dumps(js, indent=4)

    lat = js["results"][0]["geometry"]["location"]["lat"]
    lng = js["results"][0]["geometry"]["location"]["lng"]
    print 'lat' , lat, 'lng', lng

    location = js['results'][0]['formatted_address']
    print location
```




















