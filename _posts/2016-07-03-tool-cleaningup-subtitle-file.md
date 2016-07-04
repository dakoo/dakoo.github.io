---
layout: post
title: 자작 툴 - 자막 파일을 text 파일로 바꾸어주는 파이썬 코드
description: 자작 툴 - 자막 파일을 text 파일로 바꾸어주는 파이썬 코드
modified: 2016-07-03
tags: [tool]
comments: true
image:
  feature: abstract-11.png
---
영어 공부를 위해 smi나 srt 자막 파일을 text 파일로 바꿔야 할 경우가 종종 있다. 
이를 위해 간단한 파이썬 코드를 작성해 보았다. 

### 사용법

파이썬을 설치한 후 자막이 있는 폴더에 process.py파일을 만들고 아래 코드를 붙여 넣기 한 후 아래 명령을 사용한다. 

> python ./process.py [원 자막파일 명] [출력 파일]

### 코드

process.py 파일 

```python
import sys

total = len(sys.argv)
if total != 3:
    print "USE: ./process.py input_file output_file"
    exit(0)

input_file_name = sys.argv[1]
output_file_name = sys.argv[2]

f_out = open(output_file_name, 'w')

with open(input_file_name) as f_in:
    #remove blank lines
    lines = filter(None, (line.rstrip() for line in f_in))

    #remove unnecessary lines and write it to a new file
    for line in lines:
        if input_file_name.endswith('.smi'):
            # <SAMI>, <HEAD>, <TITLE>, <BODY>, <SYNC Start=1124> 
            if line.startswith('<') or line.startswith('{') or line.startswith('#'):
                continue
            #    color:white;
            if line.endswith(';') or line.endswith('}') or line.endswith('-->'):
                continue
            #</SAMI>, </BODY>
            if line.endswith('BODY>') or line.endswith('SAMI>'):
                continue
            if line.endswith('<br>'):
                line = line[:-4]
                line = line + ' '
                f_out.write(line)
                continue
        if input_file_name.endswith('.srt'):
            # for srt 
            if line.isdigit() and len(line) < 4:
                continue
            # 00:00:12,340 
            if line.startswith('0'):
                continue
            if line.startswith('<font'):
                continue
        f_out.write(line + '\r\n')

f_in.close()
f_out.close()

```
