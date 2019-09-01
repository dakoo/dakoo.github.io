---
layout: post
title: grep 명령 - 빈 줄이나 특정 줄 제거하기
modified: 2016-12-30
tags: [tool]
parent: Tool
nav_order: 2
---

grep 기능을 이용해 로그 분석 중 불필요한 줄을 제거하는 것은 아주 자주 사용하는 명령이다. 이를 좀 더 알아보자.

###  예제

예제로할 .bash_profile의 내용이 다음과 같다고 가정하자. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat .bash_profile
export ANDROID_HOME=/usr/local/opt/android-sdk

export PATH="/usr/local/Cellar/node/6.3.1/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*

# Setting PATH for Python 3.5
# The orginal version is saved in .bash_profile.pysave
PATH="/Library/Frameworks/Python.framework/Versions/3.5/bin:${PATH}"
export PATH

##
# Your previous /Users/hochulshin/.bash_profile file was backed up as /Users/hochulshin/.bash_profile.macports-saved_2016-07-06_at_21:09:39
##

# MacPorts Installer addition on 2016-07-06_at_21:09:39: adding an appropriate PATH variable for use with MacPorts.
export PATH="/opt/local/bin:/opt/local/sbin:$PATH"
# Finished adapting your PATH environment variable for use with MacPorts.

alias mysql='/usr/local/mysql/bin/mysql'
alias mysqladmin='/usr/local/mysql/bin/mysqladmin'
```

### grep의 -v 옵션과 -e 옵션

grep의 -v 옵션은 패턴이 일치하는 경우 그 줄을 표시하지 않는 것이다. -e 옵션은 regex를 사용한다는 것이다. 
이를 조합하여 다음과 같은 명령을 실행할 수 있다. 

#### 빈 줄 제거

빈 줄을 제거하는 옵션은 다음과 같다. 

```
grep -v -e '^$' 파일명
```

위의 예제에 적용한 것은 다음과 같다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat .bash_profile | grep -v -e '^$'
export ANDROID_HOME=/usr/local/opt/android-sdk
export PATH="/usr/local/Cellar/node/6.3.1/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*
# Setting PATH for Python 3.5
# The orginal version is saved in .bash_profile.pysave
PATH="/Library/Frameworks/Python.framework/Versions/3.5/bin:${PATH}"
export PATH
  
##
# Your previous /Users/hochulshin/.bash_profile file was backed up as /Users/hochulshin/.bash_profile.macports-saved_2016-07-06_at_21:09:39
##
# MacPorts Installer addition on 2016-07-06_at_21:09:39: adding an appropriate PATH variable for use with MacPorts.
export PATH="/opt/local/bin:/opt/local/sbin:$PATH"
# Finished adapting your PATH environment variable for use with MacPorts.
alias mysql='/usr/local/mysql/bin/mysql'
alias mysqladmin='/usr/local/mysql/bin/mysqladmin'
```

위에서 공백 줄이 있는 것은 스페이스로만 구성된 줄이 있다는 것이다. 

#### 스페이스로 구성된 줄도 함께 제거

위의 예제의 스페이스로만 구성된 줄도 함께 제거하자. 

```
grep -v -e '^\s*$' 파일명
```

공백이 0번이상 반복되어 있는 것도 제거한다는 것이다. 

위의 예제에 적용한 것은 다음과 같다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat .bash_profile | grep -v -e '^\s*$'
export ANDROID_HOME=/usr/local/opt/android-sdk
export PATH="/usr/local/Cellar/node/6.3.1/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*
# Setting PATH for Python 3.5
# The orginal version is saved in .bash_profile.pysave
PATH="/Library/Frameworks/Python.framework/Versions/3.5/bin:${PATH}"
export PATH
##
# Your previous /Users/hochulshin/.bash_profile file was backed up as /Users/hochulshin/.bash_profile.macports-saved_2016-07-06_at_21:09:39
##
# MacPorts Installer addition on 2016-07-06_at_21:09:39: adding an appropriate PATH variable for use with MacPorts.
export PATH="/opt/local/bin:/opt/local/sbin:$PATH"
# Finished adapting your PATH environment variable for use with MacPorts.
alias mysql='/usr/local/mysql/bin/mysql'
alias mysqladmin='/usr/local/mysql/bin/mysqladmin'
```

#### 특정 패턴으로 시작하는 줄 제거

패턴이 #이라고 한다면 아래와 같이하면 #으로 시작하는 줄이 제거된다. 

```
grep -v -e '^#' 파일명
```

위의 예제에 적용한 것은 다음과 같다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat .bash_profile | grep -v -e '^#'
export ANDROID_HOME=/usr/local/opt/android-sdk

export PATH="/usr/local/Cellar/node/6.3.1/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*

PATH="/Library/Frameworks/Python.framework/Versions/3.5/bin:${PATH}"
export PATH


export PATH="/opt/local/bin:/opt/local/sbin:$PATH"

alias mysql='/usr/local/mysql/bin/mysql'
alias mysqladmin='/usr/local/mysql/bin/mysqladmin'
```

#### 공백과 특정 패턴으로 시작하는 것을 모두 제거

이건 쉽다. 위의 두개를 스트림으로 연결하는 것이다. 

위의 예제에 적용한 것은 다음과 같다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat .bash_profile | grep -v -e '^\s*$' | grep -v -e '^#'
export ANDROID_HOME=/usr/local/opt/android-sdk
export PATH="/usr/local/Cellar/node/6.3.1/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*
PATH="/Library/Frameworks/Python.framework/Versions/3.5/bin:${PATH}"
export PATH
export PATH="/opt/local/bin:/opt/local/sbin:$PATH"
alias mysql='/usr/local/mysql/bin/mysql'
alias mysqladmin='/usr/local/mysql/bin/mysqladmin'
```
