---
layout: post
title: Python - csv
description: Python 
modified: 2016-04-10
tags: [python]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


python provides a very simple csv utility. Refer to the following example to understand how easily you could use it. 

### CSV file

The top row of a csv file contains column names for the following lines from second line. 

```csv
"Name", "Year","WHO region","Country","Age"
"Hochul Shin", "1990","Asia","Korea","77"
"Bryson", "2000","North America","USA","80"
...
```

### CSV Parser

DictReader function generates a directory structure for the csv file. Using the structure, you could navigate the csv very easily as below.

```python
import csv

csvfile = open('test.xml', 'rb')
reader = csv.DictReader(csvfile)

for row in reader:
	print row
	print row['Name'], row['Country'], row.get('Age')
```

### Output

```bash
{'Country': 'Korea', 'Age': '77', 'WHO region': 'Asia', 'Name': 'Hochul Shin', ' "Year"': ' "1990"'}
Hochul Shin Korea 77
{'Country': 'USA', 'Age': '80', 'WHO region': 'North America', 'Name': 'Bryson', ' "Year"': ' "2000"'}
Bryson USA 80
```
