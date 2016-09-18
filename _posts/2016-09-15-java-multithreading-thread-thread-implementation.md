---
layout: post
title: Java - (멀티쓰레딩 1) Thread 구현
description: Java - Thread 구현
modified: 2016-09-15
tags: [java]
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

## Java 쓰레드 구현

Java의 일반 쓰레드 구현 방식은 다음과 같다.  

1. Thread 클래스를 상속한 클래스의 객체를 생성과 동시에 구동
2. Thread 클래스를 상속한 클래스의 객체를 생성한 후 나중에 구동
3. Runnable 인터페이스를 구현한 클래스의 객체를 생성과 동시에 구동
4. Runnable 인터페이스를 구현한 클래스의 객체를 생성한 후 나중에 구동
5. 4번을 inline으로 구현하기 
6. 4번을 anonymous로 구현하기 
7. 4번을 Lambda로 구현하기 

4번째가 가장 많이 사용되는 방식이다. 그 이유는 다음과 같다. 

- 쓰레드 관련 코드와 태스크 관련 코드의 구현이 분리된다. 
- 쓰레드 생성과 실행 등의 제어가 더 용이하다. 

5,6,7번째의 경우에는 태스크를 별도 클래스가 아닌 함수로 구현하기 때문에 사용할 때 제약이 있다. 대신 UI 이벤트 처리등에 많이 사용된다. 

### 1. Thread 객체를 만들자 마자 실행하는 경우

Thread 클래스를 상속하여 쓰레드를 구현할 때는 3가지만 생각하면 된다. 

1. Thread class를 상속
2. public void run()을 구현
3. start()로 run() 메소드를 호출

여기서는 run() 메소드를 호출을 Thread 객체의 constructor에서 호출하는 것이다. 

예제는 다음과 같다. 

```java
import java.util.concurrent.TimeUnit;

public class FirstWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");

		new MyThreadTask();
		Thread t = new MyThreadTask();
		
		System.out.println("Main thread ends here...");
	}
}


class MyThreadTask extends Thread {
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		for(int i = 0; i<5; i++) {
			System.out.println("<" + id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000));
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
		this.start();
	}
}
```

출력은 다음과 같은데 순서는 달라질 수 있다. 

```bash
Main thread starts here...
Main thread ends here...
<1>TICK TICK 0
<2>TICK TICK 0
<2>TICK TICK 1
<1>TICK TICK 1
<1>TICK TICK 2
<2>TICK TICK 2
<2>TICK TICK 3
<1>TICK TICK 3
<1>TICK TICK 4
<2>TICK TICK 4
```

### 2. Thread 객체를 원하는 시점에 실행하는 경우

run() method를 호출을 Thread 객체를 받는 변수에서 호출하는 것이다. 좀 더 정교한 제어가 가능하다. 
아래 코드는 위와 코드와 동일하게 동작하지만, Thread 객체 바깥에서 실행을 제어한다. 

```java
import java.util.concurrent.TimeUnit;

public class SecondWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");

		new MyThreadTask().start();          //start() 호출
		Thread t = new MyThreadTask();
		t.start();					//start() 호출

		System.out.println("Main thread ends here...");
	}
}


class MyThreadTask extends Thread {
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		for(int i = 0; i<5; i++) {
			System.out.println("<" + id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000));
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
		//this.start();   	//제거 
	}
}
```

### 3. Runnable 객체를 만들자 마자 실행하는 경우


Runnable 인터페이스를 이용해 쓰레드를 구동할 때는 3가지만 생각하면 된다. 

1. Runnable 인터페이스 구현
2. public void run()을 구현
3. Thread(Runnable) 객체를 만들어 start()로 run() 메소드를 호출


1번과 같이 constructor에서 바로 실행시키는 경우이다. 차이점은 주석으로 표시해 두었다. 

```java
import java.util.concurrent.TimeUnit;

public class ThirdWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");

		new MyThreadTask();
		//Thread t = new MyThreadTask(); 	//Thread 타입 변로 받을 수 없음 
		new MyThreadTask();

		System.out.println("Main thread ends here...");
	}
}


class MyThreadTask implements Runnable {	//extends Thread --> implements Runnable
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		for(int i = 0; i<5; i++) {
			System.out.println("<" + id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000));
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
		//this.start();
		new Thread(this).start();			//Thread(Runnable).start()로 실행시킴
	}
}
```

### 4. Runnable 객체를 원하는 시점에 실행하는 경우

run() 메소드를 호출을 Runnable 레퍼런스에서 호출하는 것이다. 좀 더 정교한 제어가 가능하다. 
아래 코드는 위와 코드와 동일하게 동작하지만, Runnable 객체 바깥에서 실행을 제어한다. 

```java
import java.util.concurrent.TimeUnit;

public class FourthWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");

		new Thread(new MyThreadTask()).start(); 	//start()호출
		Thread t = new Thread(new MyThreadTask());
		t.start();	//start()호출
		
		System.out.println("Main thread ends here...");
	}
}


class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		for(int i = 0; i<5; i++) {
			System.out.println("<" + id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000));
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
		//new Thread(this).start();		//제거
	}
}
```

### 5. Runnable을 inline으로 구현하기

다음은 Runnable를 별도의 클래스 없이 inline으로 구현한 것이다. 주로 UI 이벤트 등의 처리에 많이 사용한다.  

```java
import java.util.concurrent.TimeUnit;

public class FifthWay {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");

		Runnable myThreadTask = new Runnable(){	//Runnable 객체
			@Override
			public void run(){
				for(int i = 0; i<5; i++) {
					System.out.println("TICK TICK " + i);
					try {
						TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000));
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			}
		};
		Thread thread = new Thread(myThreadTask);
		thread.start();

		System.out.println("Main thread ends here...");
	}
}
```

실행결과는 다음과 같다. 

```bash
Main thread starts here...
Main thread ends here...
TICK TICK 0
TICK TICK 1
TICK TICK 2
TICK TICK 3
TICK TICK 4
```

### 6. inline Runnable을 Anonymous로 구현

UI event등의 처리에 많이 사용하는 방식이다. 

```java
import java.util.concurrent.TimeUnit;

public class Sixth {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");

		new Thread(new Runnable(){
			@Override
			public void run(){
				for(int i = 0; i<5; i++) {
					System.out.println("TICK TICK " + i);
					try {
						TimeUnit.MICROSECONDS.sleep(200);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			}
		}).start();

		System.out.println("Main thread ends here...");
	}
}
```


### 7. Runnable Lambda로 구현하기 

위의 6번째를 Lambda로 구현한 것이다. 장황한 코드가 많이 사라졌음을 알 수 있다. 

```java
import java.util.concurrent.TimeUnit;

public class Seventh {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");

		new Thread(()->{
				for(int i = 0; i<5; i++) {
					System.out.println("TICK TICK " + i);
					try {
						TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000));
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			}
		).start();

		System.out.println("Main thread ends here...");
	}
}
```

