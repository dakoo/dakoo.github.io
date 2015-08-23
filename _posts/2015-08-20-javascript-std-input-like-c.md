---
layout: post
title: Javascript에서 알고리즘 문제 풀이위한 input 구현
description: Javascript에서의 알고리즘 문제 풀이위한 input 구현
modified: 2015-08-20
tags: [javascript]
image:
  feature: abstract-6.png
---

Javascript을 이용해 자료구조나 알고리즘 문제를 풀어보려하니 가장 문제되는 것이 파일로 된 샘플 입력을 받아들이고 나중에 용이하게 출력하는 것이다. 


### C에서의 샘플 입력 처리 (VisualStudio)

각 테스트 케이스 별로 N개의 입력을 숫자 쌍과 N개의 character를 입력 받아 만약 character가 '*'이면 두 숫자를 곱하고, '+'이면 두 숫자를 더한 후, N개의 결과를 모두 더해서 출력하는 것을 구현해보자. 

#### 샘플 입력 

샘플 입력은 맨 처음은 테스트 케이스의 수, N개의 숫자 쌍, N개의 character가 순서대로 나온다. 

{% highlight bash %}
2        <- testcase 수
2        <- N
1 1 2 2  <- N개의 숫자 쌍 나열
**       <- N개의 character
5
1 2 3 4 5 6 7 8 9 0 
++++++++++
{% endhighlight %}

#### 출력

{% highlight bash %}
#1 5
#2 45
{% endhighlight %}


#### VisualStudio에서의 C 구현

VisualStudio의 C 또는 C++ project에서 디버깅 옵션으로 파일 입력을 받는 것으로 설정한 후 위의 샘플입력을 처리하는 것은 다음과 같다. 

{% highlight c %}
#include <stdio.h>

#define MAX_SIZE 100
int A[MAX_SIZE];
int B[MAX_SIZE];

int main(int argc, const char * argv[]) {
    int T;
    scanf("%d", &T);
    for(int tc = 1; tc<=T; tc++){
        int N;
        scanf("%d", &N);
        for(int i = 0; i< N; i++){
            scanf("%d %d", &A[i], &B[i]);
        }
        int sum = 0;
        for(int i = 0; i< N; i++){
            char op;
            scanf("%c", &op);
            if(op == '*')
                sum += A[i]*B[i];
            else
                sum += A[i]+B[i];
        }
        printf("#%d: %d\n", tc, sum);
    }
    return 0;
}
{% endhighlight %}

### Javascript(Node.js)에서의 샘플 입력 처리 구현

{% highlight javascript %}
function Input(filename){
	var fs = require('fs');
	this.array = fs.readFileSync(filename).toString().split(/[\s,]+/);
	this.wordindex = -1;
	this.charcount = -1;

	this.s = function(){
		this.wordindex++;
		var str = this.array[this.wordindex];
		this.charcount = str.length;
		return str;
	};

	this.c = function(){
		this.charcount++;
		if(this.charcount >= this.array[this.wordindex].length){
			this.wordindex++;
			this.charcount = 0;
		}
		var str = this.array[this.wordindex];
		var c = str[this.charcount];
		return c;
	}

	this.d = function(){
		this.wordindex++;
		var str = this.array[this.wordindex];
		this.charcount = str.length;
		var num = Number(str);
		return num;
	};
}
{% endhighlight %}

### 테스트

nodejs 프로젝트를 만든 후 위의 샘플 파일을 프로젝트 폴더에 넣고 index.js에 다음 내용과 위에서 구현한 Input function을 넣고 테스트 한다. 

{% highlight javascript %}
var fin = new Input("sample.txt");
var T = fin.d();
for(var tc = 1; tc<= T; tc++){
	var N = fin.d();
	var A = [];
	var B = [];
	for(var i = 0; i< N; i++){
		A[i] = fin.d();
		B[i] = fin.d();
	}
	var sum = 0;
	for(var i = 0; i< N; i++){
		if(fin.c() == '*')
			sum += A[i]*B[i];
		else
			sum += A[i]+B[i];
	}
	console.log("#"+ tc + " " + sum);
}
{% endhighlight %}
