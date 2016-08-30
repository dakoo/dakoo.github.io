---
layout: post
title: C++ - file IO
description: file IO
modified: 2016-02-22
tags: [c++]
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


c++의 간단한 file io를 알아보자. file을 read하고 write하기 위해서는 `<fstream>`을 include해야 한다. 
ifstream은 인풋 파일, ofstream은 아웃풋 파일에 대응되는 객체이다. 그리고 fstream으로 정의하면 ifstream과 ofstream을 동시에 대응한다. 

### ifstream/ofstream 주요 함수

- constructor(argument는 string 타입의 file명) : file을 open한다. 
- is_open() : 파일이 열려 있으면 true
- close() : 파일 닫기

#### ifstream 함수

- getline(ifstream, string): ifstream 객체의 한 줄을 읽는다. 만약 file의 끝을 만나면 false

#### ofstream 함수

- << : 파일에 쓰기 

#### 코드

```
#include <iostream>
#include <fstream>
#include <string>
using namespace std;
int main(){
	string file_name = "test.txt";
	//write
	ofstream ofile(file_name);
	if (ofile.is_open()){
		ofile << "first line: " << 0 << endl;
		ofile << "second line: " << 1 << endl;
		ofile.close();
	}
	//read
	ifstream ifile(file_name);
	string line;
	if (ifile.is_open()){
		while (getline(ifile, line))
			cout << line << endl;
		ifile.close();
	}
	return 0;
}
``` 
