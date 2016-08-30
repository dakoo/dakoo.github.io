---
layout: post
title: Design - abstract factory pattern
description:  abstract factory pattern
modified: 2016-02-14
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


Abstract factory 패턴은 concrete class들을 명시하지 않고 관련 object들을 생성하는 interface를 제공한다. 
아래 코드가 그 좋은 예를 보여준다. 이 코드는 [여기](http://www.bogotobogo.com/DesignPatterns/abstractfactorymethod.php)에서 가져온 것이다. 

### Code

```
#include <iostream>
using namespace std;
class Button {
public:
	virtual void paint() = 0;
};
class WinButton : public Button {
public:
	void paint (){
		cout << " Window Button \n";
       }
};
class MacButton : public Button {
public:
	void paint (){
		cout << " Mac Button \n";
       }
};
class ScrollBar {
public:
	virtual void paint() = 0;
};
class WinScrollBar : public ScrollBar 
{
public:
	void paint (){
		cout << " Window ScrollBar \n";
       }
};
 
class MacScrollBar : public ScrollBar {
public:
	void paint (){
		cout << " Mac ScrollBar \n";
       }
};

class GUIFactory {
public:
	virtual Button* createButton () = 0;
	virtual ScrollBar* createScrollBar () = 0;
};
 
class WinFactory : public GUIFactory {
public:
	Button* createButton (){
		return new WinButton;
	}
	ScrollBar* createScrollBar (){
		return new WinScrollBar;
	}
};
 
class MacFactory : public GUIFactory {
public:
	Button* createButton (){
		return new MacButton;
	}
	ScrollBar* createScrollBar (){
		return new MacScrollBar;
	}
};
 
int main() {
	GUIFactory* guiFactory;
	Button *btn;
	ScrollBar *sb;

	guiFactory = new MacFactory;
	btn = guiFactory->createButton();
	btn -> paint();
	sb = guiFactory->createScrollBar();
	sb -> paint();

	guiFactory = new WinFactory;
	btn = guiFactory->createButton();
	btn -> paint();
	sb = guiFactory->createScrollBar();
	sb -> paint();

	return 0;
}
```
