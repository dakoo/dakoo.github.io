---
layout: post
title: python - Extract texts from a Subtitle file 
description: python Extract texts from a Subtitle file 
modified: 2016-07-02
tags: [python]
comments: true
image:
  feature: abstract-11.png
---
Study English using American soup opera is very effective and fun. To support that, let's extract texts from the smi or srt files.

## Code

```python
import sys

# Validate the arguments
total = len(sys.argv)
if total != 3:
    print "USE: ./process.py input_file output_file"
    exit(0)

input_file_name = sys.argv[1]
output_file_name = sys.argv[2]

# Output file
f_out = open(output_file_name, 'w')

# Process with opening the input file.
with open(input_file_name) as f_in:
    #!remove blank lines!
    lines = filter(None, (line.rstrip() for line in f_in))

    #remove unnecessary lines and write it to a new file
    for line in lines:
        if input_file_name.endswith('.smi'):
            # Ignore tags such as <SAMI>, <HEAD>, <TITLE>, <BODY>, <SYNC Start=1124> 
            if line.startswith('<') or line.startswith('{') or line.startswith('#'):
                continue
            # Ignore some trivial attributes such as 'color:white;'
            if line.endswith(';') or line.endswith('}') or line.endswith('-->'):
                continue
            # Ignore </SAMI>, </BODY>
            if line.endswith('BODY>') or line.endswith('SAMI>'):
                continue
            # Merge two lines that were distinguished by a '<br>' tag
            if line.endswith('<br>'): 
                line = line[:-4]
                line = line + ' '
                f_out.write(line)
                continue
                
        if input_file_name.endswith('.srt'):
            # Ignore the numeric ID of each text
            if line.isdigit() and len(line) < 4:
                continue
            # Ignore the time indicator such as '00:00:12,340' 
            if line.startswith('0'):
                continue
            # Ignore some trivial attributes such as '<font>~</font>'
            if line.startswith('<font'):
                continue
                
        f_out.write(line + '\r\n')

f_in.close()
f_out.close()

```