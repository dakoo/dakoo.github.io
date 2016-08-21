---
layout: post
title: Python - xml
description: dev
modified: 2016-04-11
tags: [python]
comments: true
image:
  feature: algorithm.jpeg
---
Python provides a very nice XML parser. With the following example, you could immediately understand how you use it.

### XML file 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<top>
	<foo name="Foo" color="green"> 123
		<goo name="Goo1">"Whereever</goo>
		<goo name="Goo2">"Whenever</goo>
		<goo name="Goo3">"Whatever</goo>
		<note name="note">"This is note</note>
	</foo>
</top>
```

### Python Example

```python
from xml.etree import ElementTree as ET 

tree = ET.parse("test.xml")
#Get the root item
root = tree.getroot()

#Get the value
print root.text #Nothing

#Find a child item by the name
foo = root.find("foo")
print foo.text #123

''' Attribute is a directory data structure '''
print foo.attrib #{'color': 'green', name':"Foo"}

#Iterate attributes 
for k, v in foo.attrib.items():
	print k, v  
print foo.attrib.keys()[0] # 'color'
print foo.attrib.values()[0] #'green'

#Get attribute value by the key
print foo.get("color") #green
print foo.attrib["color"] #green 

''' iterate children items in varous ways'''
#Iterate all 'goo' children items
goos = foo.findall("goo")
for item in goos:
	print item.text

#Iterate all children items
for item in foo:
	print item.text

#Get children and iterate them
for child in foo.getchildren():
	print child.text

```

### Output

```bash

123
------------------------------------
{'color': 'green', 'name': 'Foo'}
color green
name Foo
color
green
green
green
------------------------------------
"Whereever
"Whenever
"Whatever
------------------------------------
"Whereever
"Whenever
"Whatever
"This is note
------------------------------------
"Whereever
"Whenever
"Whatever
"This is note
```
