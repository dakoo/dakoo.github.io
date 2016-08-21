---
layout: post
title: Ubuntu - how to shorten bash prompt on ubuntu
description: Ubuntu - how to shorten bash prompt
modified: 2016-05-10
tags: [ubuntu]
comments: true
image:
  feature: abstract-12.png
---
We could be faced with very long bash prompt on ubuntu as the following example.

```
hochulshin@hochulshin-linux:~/Projects/AnIntroductionToFlask/oreilly-intro-to-flask-video$ 
```

Then, we need to shorten the bash prompt. Let's figure out how to do it.


## For immediate effect but just once

On terminal, just type the following command and enter. It works promptly.

> PS1='\u:\W\$$ '

The long bash prompt above becomes as below.

```
hochulshin:oreilly-intro-to-flask-video$$ 
```

or you can apply the following command.

> PS1='$ '

It makes the long line as below

```
$
```

You may need to figure out where you are using the `pwd` bash command.

## For permanent effect

You can add the following option in your .bash_profile or .bashrc file.

> PS1='\u:\W\$$ '

Then, relaunch the terminal.

## Reference

You can refer to [here](http://www.linuxselfhelp.com/howtos/Bash-Prompt/Bash-Prompt-HOWTO-2.html) for further detailed information.
