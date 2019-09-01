---
layout: post
title: Java - (멀티쓰레딩 3) 쓰레드 이름 설정
modified: 2016-09-17
tags: [java]
---

## Java 쓰레드 이름 설정

디버깅의 편리를 위해 Java 쓰레드에 이름을 부여하는 것이 좋다. 그 방법을 알아보자. 

### 1. 실행 시 쓰레드 이름 부여하기 

run() 메소드안에서 `Thread.currentThread().setName()`을 이용해서 쓰레드 이름을 부여한다. 이 방식은 일반 thread는 물론이고 Executor에도 동일하게 적용되는 방식이다. 

```java
import java.util.concurrent.TimeUnit;

public class NamingThreadsFirstWay {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");
		
		Thread t = new Thread(new MyThreadTask());
		t.start();

		System.out.println("Main thread ends here...");
	}
}

class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		Thread.currentThread().setName("MyThreadTask");	//setName()
		String threadName = Thread.currentThread().getName(); //getName()

		for(int i = 0; i<5; i++) {
			System.out.println("<" + threadName + ", " + id+ ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
	}
}
```

실행 결과는 다음과 같다. 

```bash
Main thread starts here...
Main thread ends here...
<MyThreadTask, 1>TICK TICK 0
<MyThreadTask, 1>TICK TICK 1
<MyThreadTask, 1>TICK TICK 2
<MyThreadTask, 1>TICK TICK 3
<MyThreadTask, 1>TICK TICK 4
```

### 2. 생성 시점에 부여하기 

Thread() constructor의 두번째 아규먼트로 이름을 입력한다. 

```java
import java.util.concurrent.TimeUnit;

public class NamingThreadsSecondWay {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");
		
		Thread t = new Thread(new MyThreadTask(), "MyThreadTask"); //이름 추가
		t.start();

		System.out.println("Main thread ends here...");
	}
}

class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		//Thread.currentThread().setName("MyThreadTask");	//제거
		String threadName = Thread.currentThread().getName();

		for(int i = 0; i<5; i++) {
			System.out.println("<" + threadName + ", " + id+ ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
	}
}
```

결과는 앞의 예제와 동일하다. 

```bash
Main thread starts here...
Main thread ends here...
<MyThreadTask, 1>TICK TICK 0
<MyThreadTask, 1>TICK TICK 1
<MyThreadTask, 1>TICK TICK 2
<MyThreadTask, 1>TICK TICK 3
<MyThreadTask, 1>TICK TICK 4
```

### 3. Executor의 쓰레드 생성시 이름 부여하기

Executor 프레임웍에서 쓰레드에 이름을 부여하고 디버깅을 하려면 ThreadFactory 인터페이스를 구현해야 한다. 
ThreadFactory 인터페이스는 newFixedThreadPool(), newCachedThreadPool(), newSingleThreadExecutor()의 아규먼트롤 사용 가능하며 다음의 메소드를 구현해야 한다. 

- 	public Thread newThread(Runnable r);

Runnable의 객체를 받아서 new Thread를 생성하여 반환하면 되는데 이때 Thread constructor에 이름을 추가하여 반환하면 Executor가 생성한 쓰레드에 이름이 할당된다. 다음이 그 예이다. 


```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.TimeUnit;

public class NamingExecutorThreads {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");
		
		ExecutorService execService = Executors.newFixedThreadPool(2, new NamedThreadsFactory()); 

		execService.execute(new MyThreadTask());
		execService.execute(new MyThreadTask());
		execService.execute(new MyThreadTask());
		execService.execute(new MyThreadTask());

		execService.shutdown();
		System.out.println("Main thread ends here...");
	}
}

class NamedThreadsFactory implements ThreadFactory {
	
	private static int count = 0;			
	private static String Name = "MyThread-";	

	@Override
	public Thread newThread(Runnable r) {
		return new Thread(r, Name + ++count); //Mythread-x 형태로 쓰레드 이름 설정
	}
}

class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	@Override
	public void run(){
		String threadName = Thread.currentThread().getName();

		for(int i = 0; i<5; i++) {
			System.out.println("<" + threadName + ", " + id+ ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep((long)Math.random()*1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public MyThreadTask() {
		this.id = ++count;
	}
}
```

결과는 다음과 같다. 

```bash
Main thread starts here...
<MyThread-1, 1>TICK TICK 0
<MyThread-2, 2>TICK TICK 0
Main thread ends here...
<MyThread-1, 1>TICK TICK 1
<MyThread-2, 2>TICK TICK 1
<MyThread-2, 2>TICK TICK 2
<MyThread-2, 2>TICK TICK 3
<MyThread-2, 2>TICK TICK 4
<MyThread-1, 1>TICK TICK 2
<MyThread-2, 3>TICK TICK 0
<MyThread-2, 3>TICK TICK 1
<MyThread-2, 3>TICK TICK 2
<MyThread-1, 1>TICK TICK 3
<MyThread-2, 3>TICK TICK 3
<MyThread-2, 3>TICK TICK 4
<MyThread-1, 1>TICK TICK 4
<MyThread-2, 4>TICK TICK 0
<MyThread-2, 4>TICK TICK 1
<MyThread-2, 4>TICK TICK 2
<MyThread-2, 4>TICK TICK 3
<MyThread-2, 4>TICK TICK 4
```

