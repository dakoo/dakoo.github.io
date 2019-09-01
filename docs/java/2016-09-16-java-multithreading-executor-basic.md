---
layout: post
title: Java - (멀티쓰레딩 2) Executor 기본
modified: 2016-09-16
tags: [java]
parent: Java
nav_order: 2
---

## Java Executor

### 1. 개요

[Java의 일반 쓰레드 구동 방식](http://hochulshin.com/java-thread-implementation/)은 다양하지만, Executor는 단지 하나의 방식으로 간단하게 쓰레드를 구동할 수 있다. 
아래의 두 가지를 하면 된다. 

- 태스크를 정의한 클래스의 생성
- Executor Service에 태스크 객체를 제공

Executor 프레임웍은 다음과 같은 특징을 지닌다. 

- 쓰레드 풀을 사용
- 무거운 쓰레드는 미리 할당 가능 
- 태스크와 쓰레드를 생성하고 관리하는 것을 분리
- 쓰레드 풀안의 쓰레드는 한번해 하나씩 여러 태스크를 실행
- 태스크 큐를 이용해 태스크를 관리
- Executor Service를 더이상 필요 없으면 중지
- Executor Service가 멈추면 모든 쓰레드도 중지 

### 2. 주요 클래스와 인터페이스

- Executor 인터페이스: 태스크와 쓰레드를 분리하고 실행을 담당 

```java
void execute(Runnable task);
```

- ExecutorService 인터페이스: Executor 인터페이스를 확장하며 라이프 사이클을 제어

```java
<T> Future <T> submit(Callable<T> task);
Future <?> submit(Runnable task); 
void shutdown();
List<Runnable> shutdownNow();
boolean isShutdown();
...
```

- Executors 클래스:  다양한 executor서비스의 인스턴스를 생성하는 Factory 클래스.


```java
public static ExecutorService newFixedThreadPool(int nThreads);
public static ExecutorService newCachedThreadPool();
public static ExecutorService newSingleThreadExecutor();
public static ExecutorService newSingleThreadScheduledExecutor();
...
```

- Future 인터페이스: 태스크가 중지되었는지 아닌지를 확인하거나 태스크로부터 응답 획득

```java
v get() throws InterruptedException, ExcutionException;
boolean isDone();
...
```

### 3. Executor를 이용한 쓰레드 구현

[Java의 일반 쓰레드 구동 방식](http://hochulshin.com/java-thread-implementation/)에 사용된 에를 그대로 이용해 보자. 일반 Thread 클래스 대신에 Executor 프레임웍을 쓴 것은 다음과 같다. 

#### 3.1 FixedThreadPool 사용 

FixedThreadPool을 이용한 예제는 다음과 같다. 

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class UsingFixedThreadPool {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");
		
		ExecutorService execService = Executors.newFixedThreadPool(2); 
		
		execService.execute(new MyThreadTask());
		execService.execute(new MyThreadTask());
	
		execService.shutdown();
		
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

MyThreadTask는 그대로이고 Thread를 이용해 Runnable 객체를 구동하는 것을 ExecutorService로 대체했다. 
그리고 반드시 **shutdown()**을 해줘야만 thread가 모두 중지된다. 만약 빼먹으면 leakage가 발생하는 것이니 주의하자. 실행 결과는 다음과 같다. 

```bash
Main thread starts here...
<1>TICK TICK 0
<2>TICK TICK 0
Main thread ends here...
<1>TICK TICK 1
<1>TICK TICK 2
<2>TICK TICK 1
<1>TICK TICK 3
<1>TICK TICK 4
<2>TICK TICK 2
<2>TICK TICK 3
<2>TICK TICK 4
```

그런데 아래와 같이 ThreadPool의 숫자는 2개로 유지한 채 태스크를 실행하는 수를 4번으로 2배 늘리면 어떻게 될까? 


```java
ExecutorService execService = Executors.newFixedThreadPool(2); 
		
execService.execute(new MyThreadTask());
execService.execute(new MyThreadTask());
execService.execute(new MyThreadTask());
execService.execute(new MyThreadTask());
			
execService.shutdown();
```

결과는 다음과 같다. 쓰레드 id 3과 4는 1과 2가 완료된 이후에야 쓰레드를 점유함을 알 수 있다.

```bash
Main thread starts here...
<2>TICK TICK 0
Main thread ends here...
<1>TICK TICK 0
<1>TICK TICK 1
<1>TICK TICK 2
<1>TICK TICK 3
<2>TICK TICK 1
<1>TICK TICK 4
<2>TICK TICK 2
<3>TICK TICK 0
<3>TICK TICK 1
<2>TICK TICK 3
<3>TICK TICK 2
<2>TICK TICK 4
<3>TICK TICK 3
<4>TICK TICK 0
<3>TICK TICK 4
<4>TICK TICK 1
<4>TICK TICK 2
<4>TICK TICK 3
<4>TICK TICK 4
```

#### 3.2 CachedThreadPool 사용

CachedThreadPool은 FixedThreadPool과 달리 태스크의 숫자에 따라 쓰레드의 숫자가 가변된다는 것이다.
그러므로 생성시 쓰레드 숫자를 지정할 필요가 없다. 

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class UsingCachedThreadPool {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");
		
		//ExecutorService execService = Executors.newFixedThreadPool(2); 
		ExecutorService execService = Executors.newCachedThreadPool(); 
		
		execService.execute(new MyThreadTask());
		execService.execute(new MyThreadTask());
	
		execService.shutdown();
		
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

실행 결과는 다음과 같이 newFixedThreadPool을 사용했을 때와 동일하다. 

```bash
Main thread starts here...
<1>TICK TICK 0
<2>TICK TICK 0
Main thread ends here...
<1>TICK TICK 1
<2>TICK TICK 1
<2>TICK TICK 2
<2>TICK TICK 3
<2>TICK TICK 4
<1>TICK TICK 2
<1>TICK TICK 3
<1>TICK TICK 4
```

이제, 아래와 같이 태스크를 실행하는 수를 4번으로 2배 늘려보자.  


```java
ExecutorService execService = Executors.newCachedThreadPool(); 
		
execService.execute(new MyThreadTask());
execService.execute(new MyThreadTask());
execService.execute(new MyThreadTask());
execService.execute(new MyThreadTask());
			
execService.shutdown();
```

결과는 다음과 같다. 쓰레드 id 1,2,3,4가 섞여서 실행되고 있음을 알 수 있다. 즉, 가변적으로 쓰레드 숫자가 늘어난 것이다. 

```bash
Main thread starts here...
<2>TICK TICK 0
<1>TICK TICK 0
<3>TICK TICK 0
<4>TICK TICK 0
Main thread ends here...
<2>TICK TICK 1
<2>TICK TICK 2
<2>TICK TICK 3
<3>TICK TICK 1
<4>TICK TICK 1
<3>TICK TICK 2
<1>TICK TICK 1
<2>TICK TICK 4
<1>TICK TICK 2
<3>TICK TICK 3
<4>TICK TICK 2
<3>TICK TICK 4
<1>TICK TICK 3
<4>TICK TICK 3
<4>TICK TICK 4
<1>TICK TICK 4
```

#### 3.3 SingleThreadExecutor

이것은 이전에 다룬 Executor와 동일한데, 단지 쓰레드의 숫자가 단지 하나이고 하나의 태스크가 완료된 이후에 다음 태스크가 실행된다. 
이것을 사용하면 동기화를 고려해야 할 필요없이, 즉 synchronized를 사용하지 않고도 안전하게 실행할 수 있다. 

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class UsingSingleThreadExecutor {
	public static void main(String argc[]) {
		System.out.println("Main thread starts here...");
		
		ExecutorService execService = Executors.newSingleThreadExecutor(); 
		
		execService.execute(new MyThreadTask());
		execService.execute(new MyThreadTask());

		execService.shutdown();
		
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

실행 결과는 다음과 같이 하나의 태스크가 완전히 실행된 후에야 다음 태스크가 실행된다. 

```bash
Main thread starts here...
Main thread ends here...
<1>TICK TICK 0
<1>TICK TICK 1
<1>TICK TICK 2
<1>TICK TICK 3
<1>TICK TICK 4
<2>TICK TICK 0
<2>TICK TICK 1
<2>TICK TICK 2
<2>TICK TICK 3
<2>TICK TICK 4
```
