---
layout: post
title: How to install eclipse on ubuntu 14.04
description: How to install eclipse on ubuntu 14.04
modified: 2016-07-24
tags: [dev]
comments: true
image:
  feature: abstract-21.png
---
Let's install the eclipse on ubuntu 14.04

1. Move to [the eclipse web page](www.eclipse.org/) and download the latest version of the eclipse. Note that you check 32-bit or 64-bit.
2. After completing the download, open a terminal and move to the folder where the file was downloaded for example ~/Downloads.
3. tar -zxvf [the eclipse file] /opt
4. sudo ln -s /opt/[eclipse folder]/eclipse /usr/local/bin
5. Create a new file eclipse.desktop in /usr/share/applications/ and add the below code. Then 'sudo desktop-file-install /usr/share/applications/eclipse.desktop'

```
[Desktop Entry]
Name=Eclipse
Type=Application
Exec=/opt/[eclipse folder]/eclipse
Terminal=false
Icon=/opt/[eclipse folder]/icon.xpm
Comment=Integrated Development Environment
NoDisplay=false
Categories=Development;IDE;
Name[en]=eclipse.desktop
```

For Eclipse icon to be displayed in dash, run following command:

- sudo cp /opt/eclipse/icon.xpm /usr/share/pixmaps/eclipse.xpm
