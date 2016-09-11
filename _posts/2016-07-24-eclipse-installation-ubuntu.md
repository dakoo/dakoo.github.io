---
layout: post
title: Ubuntu, Java - How to install eclipse on ubuntu 14.04
description: Ubuntu, Java - How to install eclipse on ubuntu 14.04
modified: 2016-07-24
tags: [ubuntu, java, eclipse]
comments: true
image:
  feature: abstract-21.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Let's install the eclipse on ubuntu 14.04

## Installation

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

## Configuration

### VIM 

1. Menu > Help > Install New Software... > Add button
2. Name: vim, Location: http://vrapper.sourceforge.net/update-site/stable
3. Select all > Next ...
4. Accept the license agreement > Finish

### For Web Projects

1. Menu > Help > Install New Software... 
2. The drop down menu of Work with
3. Select the corresponding version. For example, mars if the version of eclipse is mars(4.5)
4. In the box of software list, find 'Web, XML, Java EE and OSGi Enterprise Development'.
5. Select Eclipse Java EE Developer Tools, Eclipse java Web Developer Tools Eclipse Web Developer Tools, and JavaScript Development Tools
6. Install them and accept the term of license agreement. 

### For the AWS Toolkit for Eclipse

1. Menu > Help > Install New Software... > Add button
2. Name: aws, Location: https://aws.amazon.com/eclipse
3. Select all > Next ...
4. Accept the license agreement > Finish

#### Register AWS Key ID

##### Create AWS Key

1. Open the AWS > IAM console.
2. From the navigation menu, click Users.
3. Select your IAM user name.
4. Click User Actions, and then click Manage Access Keys.
5. Click Create Access Key. Now you get the Access Key. 

##### Register AWS Key in the eclipse

1. Open Eclipse’s Preferences dialog box and click AWS Toolkit in the sidebar.
2. Type or paste your AWS access key ID in the Access Key ID box.
3. Type or paste your AWS secret access key in the Secret Access Key box.
4. Click Apply or OK to store your access key information.
