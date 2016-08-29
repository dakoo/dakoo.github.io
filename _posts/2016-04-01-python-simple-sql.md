---
layout: post
title: Python - simple sql 
description: Python - simple sql  
modified: 2016-04-01
tags: [python, database]
comments: true
image:
  feature: abstract-11.png
---
Python SQL is very simple and powerful. Let's learn python-SQL using several examples
This code is used for an assignment for [Coursera: Using Databases with Python](https://www.coursera.org/learn/python-databases/home/welcome). For furhter information, check the [Coursera: Using Databases with Python](https://www.coursera.org/learn/python-databases/home/welcome) out. 

### Count frequency using database

```python
import urllib
import sqlite3
conn = sqlite3.connect('emaildb.sqlite')
cur = conn.cursor()

cur.execute('DROP TABLE IF EXISTS Counts')
cur.execute('CREATE TABLE Counts (org TEXT, count INTEGER)')

url = raw_input('Enter file name:') or "http://www.pythonlearn.com/code/mbox.txt"
f = urllib.urlopen(url)

for line in f:
	if not line.startswith('From') : continue
	if line.startswith('From:') : continue
	words = line.split()
	email = words[1].split('@')
	org = email[1]
	cur.execute('SELECT count FROM Counts WHERE org = ?', (org,))
	row = cur.fetchone()
	if row is None:
		cur.execute('INSERT INTO Counts (org, count) VALUES (?, 1)', (org,))
	else:
		cur.execute('UPDATE Counts SET count=count+1 WHERE org = ?', (org, ))
	conn.commit()

print "Counts:"
for row in cur.execute('SELECT org, count FROM counts ORDER BY count DESC LIMIT 10'):
	print str(row[0]), row[1]

cur.close()
```

### Count frequency using directory

The above code has the same functionality as the following code.

```python
import urllib

url = raw_input('Enter file name:') or "http://www.pythonlearn.com/code/mbox.txt"
f = urllib.urlopen(url)

orgs = {}
for line in f:
	if not line.startswith('From') : continue
	if line.startswith('From:') : continue
	words = line.split()
	email = words[1].split('@')
	org = email[1]
	orgs[org] = orgs.get(org, 0) + 1

lst = sorted(orgs.iteritems(), key= lambda (k,v): (v,k), reverse = True)
print "Counts:"
for k, v in lst[:10]:
	print k, v
```
