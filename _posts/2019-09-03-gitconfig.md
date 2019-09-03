---
layout: post
title: Tool - git global configuration
modified: 2019-09-03
tags: [tool]
---

나의 git globacl configuration. commit, checkout, cherry-pick 대신 ci, co, cp등을 사용하도록 하는 간단한 configuration

```
[user]
	email = {EMAIL}
	name = {NAME}
[color]
	ui = true
[color "branch"]
    current = yellow reverse
    local = yellow
    remote = green
[color "status"]
    added = yellow
    changed = green
    untracked = cyan
[color "diff"]
    whitespace = red reverse
	meta = blue black bold
[core]
	pager = less -F -XR -x2
	excludesfile = /{User Home}/.gitignore_global
[push]
	default = tracking
[receive]
	denyCurrentBranch = warn
[branch]
	autosetupmerge = true
[alias]
	dag = log --graph --format='format:%C(yellow)%h%C(reset) %C(blue)\"%an\" <%ae>%C(reset) %C(magenta)%cr%C(reset)%C(auto)%d%C(reset)%n%s' --date-order
[commit]
	template = /{User Home}/.stCommitMsg
[amazon]
	append-cr-url = true
	pull-request-by-default = true
	pull-request-using-local-branch-name = true
[alias]
    st = status
    ts = status
    ci = commit
    br = branch
    co = checkout
    cp = cherry-pick 
    rb = rebase
    irb = rebase -i HEAD~3
    unstage = reset HEAD --
    diffstat = diff -w --ignore-space-at-eol --stat -r
    h = log --graph --all --decorate --since='2 week' --format=format:'%C(auto)%d%C(reset) %C(dim cyan)%h%C(reset)  %C(dim green)(%ar)%C(reset) %C(dim yellow)%an%C(reset)   %C(white)%s%C(reset)'
    hl = log --graph --all --decorate --format=format:'%C(auto)%d%C(reset) %C(dim cyan)%h%C(reset)  %C(dim green)(%ar)%C(reset) %C(dim yellow)%an%C(reset)   %C(white)%s%C(reset)'
    history = !"git lg1-specific"
```
