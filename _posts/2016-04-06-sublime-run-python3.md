---
layout: post
title: sublime text3 configuration to run python3
description: dev
modified: 2016-04-06
tags: [dev]
comments: true
image:
  feature: algorithm.jpeg
---
Without any user-specific configuration, sublime-text runs *.py files on python2. Therefore, it's painful to run *.py written in python3.
In case, refer to the following configuration. The explanation assumes the underlying OS is ubuntu 14.04. If the OS is different, you should change the path of python3.

### sublime-text3 configuration

On a terminal, check the path of python3 out using the 'which' command

```
$ which python3
/usr/bin/python3
```

Then, run the sublim-text3 and then select menu > Tools > Build System > New Build System....Enter the following on opened 'untitled.sublime-build' file.

```
{
"cmd": ["/usr/bin/python3", "$file"]
, "selector": "source.python"
, "file_regex": "file \"(...*?)\", line ([0-9]+)"
}
```

Finally, save the untitled.sublime-build file as a 'python3.sublime-build' in ../Packages/User/Python3.sublime-build. 
The directory is a default directory when you save the python3.sublime-build. 


### Test

Make a hello.py file below.

```
print("hello python3")
```

Then, on sublime-text3, run it (Menu > Tools > Build, or Control+B). If you can see the "hello python3" message, everything is perfect.
