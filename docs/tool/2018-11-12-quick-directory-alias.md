---
layout: post
title: Tool - Quick directory alias
description: Tool - Quick directory alias
modified: 2018-11-12
tags: [tool]
parent: Tool
nav_order: 2
---

mac이나 linux의 터미널에서 directory 이동을 편리하게 해주는 [quick-directory-alias](https://github.com/mcwoodle/shell-directory-management/blob/master/README.md)에 몇가지 기능을 추가해서 사용하고 있다. 나름 만족해서 사용하고 있어서 블로그를 통해 공유한다. 

## 기존 기능

기존의 [quick-directory-alias](https://github.com/mcwoodle/shell-directory-management/blob/master/README.md)는 directory의 alias의 추가, 삭제, 확인이 가능하고, 무엇보다 그 alias로 바로 jump하는데 유용하다. alias의 자동완성 또한 지원한다. 

## 강화된 기능

기존 스크립트로도 사실 잘 사용하고 있지만, 자주 사용하다보니 아쉬운 기능이 있어 다음과 같은 기능을 추가했다. 

1. alias에 설명 추가: 리스트를 설명과 함께 확인할 수 있어서 선택을 용이하게 한다. 
1. alias list clear: 리스트를 완전히 지우고 다시 만들기 쉽게 했다. 
1. embedded command 실행: 내가 자주 사용하는 intellij IDEA, sourcetree, finder(mac)을 alias상에서 즉시 실행할 수 있도록 했다. 
1. custom command 실행: 기타 어떤 명령이라도 해당 alias사에서 실행 할 수 있도록 했다. 

자세한 내용은 아래 설명을 참조하자.

## git respository

https://github.com/dakoo/shell-directory-management/blob/master/README.md

## Installation

1. Download the [script](https://github.com/dakoo/shell-directory-management/blob/master/quick-directory-aliases.sh)
1. Source the script into your current shell (add this to your shell startup script to always have the command available)
1. Done

#### Quick setup commands

> Note: Before running the following, change `RC_FILE` to match your preferred shell's rc file, or any other file that you source when a new shell/terminal is created.

```bash
RC_FILE=~/.bashrc
SCRIPT_DIRECTORY=~/scripts

mkdir -p $SCRIPT_DIRECTORY
wget -O $SCRIPT_DIRECTORY/quick-directory-aliases.sh https://raw.githubusercontent.com/mcwoodle/shell-directory-management/master/quick-directory-aliases.sh
printf "\n. $SCRIPT_DIRECTORY/quick-directory-aliases.sh\n" >> $RC_FILE
. $RC_FILE
```

> The default name for the command is "d". Edit the `quick-directory-aliases.sh` script and change the function name `d()` to a name of your choosing.

## Usage

#### Add an alias
```bash
% cd /any/really/long/or/short/directory/path/thats/hardoreasy/to/remember
% d + shortAliasName
```
> Note: changes take effect immediately across terminals/shells.

Also you can add a tag to the alias.

```bash
% cd /any/really/long/or/short/directory/path/thats/hardoreasy/to/remember
% d + shortAliasName "some tag or comment"
```

#### Navigate to an alias
```bash
% d shortAliasName

% pwd
/any/really/long/or/short/directory/path/thats/hardoreasy/to/remember
```

#### Remove an alias
```bash
% d - shortAliasName
```

#### See all aliases
```bash
% d
workspace = /home/mcwoodle/workspaces/someWorkspaceDirectory -> workspace
bin = /usr/bin -> bin folder
nhl = /home/mcwoodle/go/leafs/go -> .
```

#### Clear all aliases

```bash
% d c all
```

#### Help 

```bash
% d ?
```

#### Execute a command in the alias

##### Run intellij idea in the folder

```bash
% d e workspace ij
```

##### Run sourcetree in the folder

```bash
% d e workspace st
```

##### Run finder in the folder(mac)

```bash
% d e workspace op
```

##### Custom Command in the folder

```bash
% d e workspace pwd
```

If the command consists of multiple words, wrap it with double quotes.

```bash
% d e workspace "ls -al"
```

##### Customize the embedded commands

You could modify the following block of the script.

```
    if $_d_excuteCommand;
    then
        _d_cmd=`printf "$_d_aliasRow" | sed -e "s,.* = \(.*\) -> .*,\1,"`
        printf "cd $_d_cmd\n"
        case "$2" in
            ij) /usr/local/bin/idea $_d_cmd;;
            st) stree $_d_cmd;;
            op) open $_d_cmd;;          
            *) "$2" $_d_cmd;;
        esac
        return 1
    fi
```

#### Autocomplete

Autocomplete is currently only supported using bash and zsh (or any script supporting `complete\compgen` or `compctl` built-ins).

Installation is automatic and works like any other bash/zsh tab based autocomplete.
