---
layout: post
title: python - json
description: python
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

print jobj  # {u'y': u'yes', u'x': 1, u'z': [3, 4]}
print jobj['x'] # 1
print jobj['y'] # "yes"
for i in jobj['z']:
    print i   # 3, 4
```

### json.dumps

json.dumps() returns a string from an json object. Let's look at the example below.

```javascript
jobj = {
    'x': 1, 
    'y': 'yes', 
    'z': [3, 4]
}
jstr = json.dumps(jobj)
print jstr # {"y": "yes", "x": 1, "z": [3, 4]}
```

### json.load

json.load is a kind of read function from file. json.load() returns an object from a file handler.

```javascript
''' example.json file content
  {"x" : 1, "y": "yes", "z": [3, 4]}
'''
import json

with open('example.json') as f:
  jstr = json.load(f) #instead of read()
  print jstr # {u'y': u'yes', u'x': 1, u'z': [3, 4]}
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

#The content of the example.json should be 
# {"y": "yes", "x": 1, "z": [3, 4]}
```





