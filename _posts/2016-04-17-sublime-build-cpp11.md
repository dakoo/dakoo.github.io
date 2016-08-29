---
layout: post
title: C++ Sublime text - Build c++11 on Sublime
description: C++ - sublime text3 
modified: 2015-07-04
tags: [c++, sublime]
comments: true
image:
  feature: abstract-11.png
---
Without any user-specific configuration, sublime-text compiles *.cpp files with the old cpp compiler. 
Therefore, it’s painful to use old version of cpp. In case, refer to the following configuration. 

### sublime-text3 configuration

Then, run the sublim-text3 and then select menu > Tools > Build System > New Build System….Enter the following on opened ‘untitled.sublime-build’ file.

```bash
{
    "shell_cmd": "g++ \"${file}\" -o \"${file_path}/${file_base_name}\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c, source.c++",

    "variants":
    [
        {
            "name": "Run",
            "shell_cmd": "g++ -o \"${file_path}/${file_base_name}\" -std=c++11 \"${file}\" && \"${file_path}/${file_base_name}\""
        }
    ]
}
```

Finally, save the untitled.sublime-build file as a ‘cpp.sublime-build’ in ../Packages/User/cpp.sublime-build. The directory is a default directory when you save the cpp.sublime-build. 
