---
layout: post
title: Eclipse - OSX에서의 단축키
description: Eclipse - OSX에서의 단축키
modified: 2016-09-28
tags: [osx, eclipse, java]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

OSX에서 eclipse 단축키를 정리해 보았다. vim mode를 위해 vrapper를 사용하는 경우이다. 
Windows/Ubuntu에서의 단축키 중 command -> control로 안되는 것들은 표시를 해 둔다. 그리고 binding되어 있지 않지만, 매우 중요한 키들은 시작하기 전에 미리 세팅해 놓는 것을 가정했다.

### 1. 시작하기 전

key가 binding되어 있지 않지만, binding해서 사용해야 하는 것들이 있다. 먼저 이것을 설정하자. Preferences > General > Keys이고 theme는 default.

주요 키는 control에 맵핑!

- Activate editor: control + b
- Backward History: control + h
- Forward History: control + l
- next editor: control + k
- open declaration 모두 : control + p
- open call hierachy: control + i
- Quick Fix: control + ,
- Qucik outline(Editing Java Source): control + m
- Show Tooltip description(Editing Text): control + .
- Generate Getters and Setters: option + command + f

참고로 editor 창을 분할하여 보는 것은 shortcut으로 하면 버그가 있다. 두개 이상의 파일을 연 상태에서 마우스로 하나를 선택해서 아래로 살짝 내려 세로 분할이 되도록 하자. 

### 2. OSX에서 eclipse 단축키

#### 1. 쉽게 사용하기

- **shift + command + l**: 단축키 확인
- command + 3: quick access

#### 2. 창간 이동 

- command + f7(fn + 7): view간 이동. 주로 package explorer 등으로 이동이 편함. 두개의 view간 이동시 한번만 누르면 됨
- command + f9(fn + 8): perspective간 이동. command를 누른 상태로 f8을 여러번 누르는 방식이 편함
- **control + b**: editor로 포커스 이동
- shift + command + e: 파일을 선택해서 editor로 포커스 이동

#### 3. editor내 이동

##### 3.1 파일간 이동 

- **contorl + k** : file을 선택하여 이동. 한번 누르면 이전 선택 파일로 이동
- **shift + command + t** : java type 타이핑해서 열기
- **shift + command + r** : file 이름 타이핑해서 열기
- command + e: 현재 열려있는 파일 리스트를 보여주며 화살표 위아래로 선택

##### 3.2 코드내 바른 이동

- **control + j**: 커서 히스토리에서 previous로 이동
- **control + k**: 커서 히스토리에서 next로 이동
- **control + m**: 현재 java class의 빠른 창 띄워서 method와 변수 보여주기. 키보드로 움직여서 선택하면 커서가 그 위치로 이동
- **command + .**: 하나의 파일 내에서 다음 problem(warning)/error으로 커서 이동
- shift + command + p: 괄호에서 누르면 대응하는 괄호로 이동

#### 4. 코딩시 참조하기

- **control + p**: 현재 선택한 변수의 **선언**으로 커서 이동 
- **control + i**: call hierarchy 창이 떠서 표시됨
- **control + .**: 변수등에 놓고 누르면 작은 tooltip 창이 뜸
- **contorl + ,**: 커서 위치에 해당하는 warning, error등의 quick fix 창 보여주기
- option + command + g: text를 선택한 후 누르면 일치하는 것들을 workspace에서 찾아서 Search 창에서 보여줌. Search 창에서 enter를 누르면 그 파일 열리며 커서 이동하고 그 text가 highlight되어 표시됨
- shift + command + g: text를 선택한 후 누르면 reference를 찾아서 Search 창에서 보여줌. Search 창에서 enter를 누르면 그 파일 열리며 커서 이동하고 그 text가 highlight되어 표시됨
- f4 (fn + 4): 특정 Type을 선택한 후 f4를 누르면 type Hierarchy Windows로 포커스가 이동하며 그 type의 계층 구조 보여줌 
- command + g: 클래스 변수나 메소드의 선언 확인 
- command + t: 현재 java class의 빠른 창 띄워서 상속 구조 보여 주기
- shift + f2(fn + 2): 새로운 창이 떠서 javadoc이 열림


#### 5. 코딩을 더 편리하게

- **control + space**: 자동 완성하거나 추천
- **shift + command + o**: 자동으로 import 정리하기 (import error 해결하고 불필요한 import 제거)
- **shift + command + f**: 코드 포맷팅하기
- **option + commmand + r** (w - shift + alt + r): rename
- **option + command + t** (w - shift + alt + t): refactoring 메뉴
- **option + command + m**: 여러 줄을 선택한 후 누르면 method 추출창 뜸
- **option + command + n**: eclipse 어디에서 눌러도 new package, class, interface 생성
- **option + command + c**: eclipse 어디에서 눌러도 new class
- **option + command + i**: eclipse 어디에서 눌러도 new interface
- **option + command + s**: 소스 코드 삽입 창
- **option + command + f**: 클래스 내에서 누르면 getter와 setter 자동 생성
- command + /: 주석 토글

#### 6. 저장과 실행

- command + s: 에디터 창의 현재 파일 저장
- **command + f11(fn + 11)**: main 함수가 있는 파일이 열려있다면 그 파일을, 아니면 마지막에 실행된 파일을 실행(Run)

