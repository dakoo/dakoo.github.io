---
layout: post
title: Python - excel
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


[python-excel](http://www.python-excel.org/) contains many tools to handle excel files in the Python. Xlrd is used to read and parse old excel files(.xls). With the xlrd example below, let's see how they work.

### Example of xlrd

```python
import xlrd

book = xlrd.open_workbook('../../data/chp4/SOWC 2014 Stat Tables_Table 9.xlsx')

sheet = book.sheet_by_name('Table 9 ')

data = {}
# iterate each rows
for i in xrange(14, sheet.nrows):
  # Get row as a list
	row = sheet.row_values(i)
	country = row[1]
	# Set a directory data structure with row
	data[country] = {
		'child_labor': {
			'total': [row[4], row[5]],
			'male':[row[6], row[7]],
			'female': [row[8], row[9]],
		},
		'child_marriage':{
			'married_by_15': [row[10], row[11]],
			'married_by_18': [row[12], row[13]],
		}
	}
	if country == 'Zimbabwe':
		break

# beautiful print using pprint
import pprint
pprint.pprint(data)
```
