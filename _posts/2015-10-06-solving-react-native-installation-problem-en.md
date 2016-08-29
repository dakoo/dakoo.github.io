---
layout: post
title:  Solving a react-native installation problem
modified: 2015-10-06
tags: [react.native]
comments: true
image:
  feature: abstract-12.png
---

When I executed `react-native init stopwatch` to set up a react-native project, the console didn't do anything for a while and threw the following logs.

{% highlight bash %}
Hochul-ui-MBP:projects hochulshin$ react-native init stopwatch
This will walk you through creating a new React Native project in /Users/hochulshin/projects/stopwatch
npm ERR! git clone --template=/Users/hochulshin/.npm/_git-remotes/_templates --mirror git://github.com/facebook/react.git /Users/hochulshin/.npm/_git-remotes/git-github-com-facebook-react-git-baa2986b: Cloning into bare repository '/Users/hochulshin/.npm/_git-remotes/git-github-com-facebook-react-git-baa2986b'...
npm ERR! git clone --template=/Users/hochulshin/.npm/_git-remotes/_templates --mirror git://github.com/facebook/react.git /Users/hochulshin/.npm/_git-remotes/git-github-com-facebook-react-git-baa2986b: fatal: read error: Operation timed out
npm ERR! Darwin 14.5.0
npm ERR! argv "/Users/hochulshin/.nvm/versions/node/v4.1.2/bin/node" "/Users/hochulshin/.nvm/versions/node/v4.1.2/bin/npm" "install" "--save" "react-native"
npm ERR! node v4.1.2
npm ERR! npm  v2.14.4
npm ERR! code 128

npm ERR! Command failed: git clone --template=/Users/hochulshin/.npm/_git-remotes/_templates --mirror git://github.com/facebook/react.git /Users/hochulshin/.npm/_git-remotes/git-github-com-facebook-react-git-baa2986b
npm ERR! Cloning into bare repository '/Users/hochulshin/.npm/_git-remotes/git-github-com-facebook-react-git-baa2986b'...
npm ERR! fatal: read error: Operation timed out
npm ERR!
npm ERR!
npm ERR! If you need help, you may report this error at:
npm ERR!     <https://github.com/npm/npm/issues>

npm ERR! Please include the following file with any support request:
npm ERR!     /Users/hochulshin/projects/stopwatch/npm-debug.log
`npm install --save react-native` failed
{% endhighlight %}

I tried *npm install --save react-native* again and failed.

To resolve this problem, we need to use "https" instead of "git".

{% highlight bash %}
git config --global url."https://".insteadOf git://
{% endhighlight %}

Successful!
