---
layout: post
title: Java - (멀티쓰레딩 8) 쓰레드 Exception 처리하기
modified: 2016-09-20
tags: [java]
parent: Java
nav_order: 2
---

## 1 일반 쓰레드

쓰레드에서 Exception을 던지면 main 쓰레드에서 어떻게 처리해야 할까? 단지 catch만 하면 될까? 
일반적으로 생각하는 Exception 처리의 문제점을 살펴보고 어떻게 해결해야 할지 알아보자. 

### 1.1 쓰레드의 Uncaughted Exception

쓰레드에서 동작 중에 Exception을 발생시키는 경우 어떻게 처리해야 할까? 제대로 처리하지 못하면 Exception leakage가 발생하게 되어 어플리케이션이 비정상적으로 종료되고 만다. 
쓰레드의 Exception을 처리할 때 뭐가 문제인지를 다음 2가지 예를 통해 살펴본다. 

- Exception을 처리하지 않을 때 
- 쓰레드 밖에서 쓰레드가 던진 Exception을 바로 받아서 처리할 때

#### 1.1.1 처리되지 않는 Exception

아래의 예는 쓰레드에서 RuntimeException을 던지고 아무것도 하지 않는 경우이다. 

```java
public class HandlingUncaughtExceptionsForThreads {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		new Thread(new ExceptionLeakingTask(), "Mythread-1").start();

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}
```

아래 처럼 쓰레드에서 RuntimeException을 던졌을 경우 제대로 처리하지 않으면 어플리케이션이 비정상적으로 종료된다. 

```bash
[main] thread starts here...
[main] thread ends here...
Exception in thread "Mythread-1" java.lang.RuntimeException
	at t1.ExceptionLeakingTask.run(HandlingUncaghtExceptionForEveryThread.java:22)
	at java.lang.Thread.run(Thread.java:745)
```

#### 1.1.2 try/catch 블록을 이용한 직접 Exception을 처리하기

이제 try/catch 블록을 이용해서 태스크에서 던진 RuntimeException을 아래와 같이 처리해 보자. 

```java
public class HandlingUncaughtExceptionsForThreads {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		try {
			new Thread(new ExceptionLeakingTask(), "Mythread-1").start();
		} catch(RuntimeException re) {
			System.out.println("!!!!!!["+ currentThreadName + "]" + " Caught Exception!!!");
		}

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}
```

결과는 다음과 같다. 위의 Exception을 처리하지 않은 것과 같은 결과를 얻었다. 특히 catch 구문은 아예 동작하지 않았다. 
이를 통해 **쓰레드가 던진 Exception을 쓰레드 바깥에서 try/catch로 바로 받을 수 없다**는 것을 알 수 있다. 

```bash
[main] thread starts here...
[main] thread ends here...
Exception in thread "Mythread-1" java.lang.RuntimeException
	at t1.ExceptionLeakingTask.run(HandlingUncaghtExceptionForEveryThread.java:22)
	at java.lang.Thread.run(Thread.java:745)
```

### 1.2 UncaughtExceptionHandler 인터페이스로 처리하기 

UncaughtException을 처리하기 위해 UncaughtExceptionHandler 인터페이스를 구현하자. 
UncaughtExceptionHandler 인터페이스는 다음의 메소드 하나만을 가진다. 

> void uncaughtException(Thread thread, Throwable e)

UncaughtExceptionHandler를 이용하는 방법은 3가지가 있다. 


#### 1.2.1 시스템 내의 모든 쓰레드를 위한 디폴트 handler 지정하기 

시스템 내의 모든 쓰레드를 위한 디폴트 handler로 지정하여 UncaughtException을 처리한다. 이를 위해 아래 메소드를 이용해 쓰레드를 시작하기 이전에 디폴트로 지정한다. 
주의할 것은 아래 메소드는 static이라는 것이다. 어디에서나 Thread.sefDefaultUncaughtExceptionHandler()을 호출할 수 있다. 

> static void Thread.setDefaultUncaughtExceptionHandler(UncaughtExceptionHandler eh)

아래의 예는 ThreadExceptionHandler 인터페이스를 구현하고 그 객체를 디폴트 handler로 등록한 것이다. 이 경우 try/catch는 필요없다. 

```java
import java.lang.Thread.UncaughtExceptionHandler;

public class HandlingUncaughtExceptionsForThreads {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread.setDefaultUncaughtExceptionHandler(new ThreadExceptionHandler("DefaultHandler")); 

		Thread thread1 = new Thread(new ExceptionLeakingTask(), "Mythread-1");
		Thread thread2 = new Thread(new ExceptionLeakingTask(), "Mythread-2");
		
		thread1.start();
		thread2.start();
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}

class ThreadExceptionHandler implements UncaughtExceptionHandler{
	private String handlerName;
	
	public ThreadExceptionHandler(String handlerName) {
		this.handlerName = handlerName;
	}

	@Override
	public void uncaughtException(Thread thread, Throwable e) {
		System.out.println(handlerName + " caught Exception in Thread - " 
			+ thread.getName()
			+ " => " + e);
	}

}

```

결과는 다음과 같다. 정상적으로 Exception이 처리되는 것을 볼 수 있다. 쓰레드 하나가 아닌 모든 쓰레드의 Exception을 처리한다. 

```bash
[main] thread starts here...
[main] thread ends here...
DefaultHandler caught Exception in Thread - Mythread-1 => java.lang.RuntimeException
DefaultHandler caught Exception in Thread - Mythread-2 => java.lang.RuntimeException
```

#### 1.2.2 쓰레드 별로 handler를 각기 지정하기  

각 쓰레드 별로 각기 다른 Exception Handler를 지정하는 방법은 아래 메소드를 이용하는 것이다. static이 아니라는 것을 명심하자. 

> void Thread.setUncaughtExceptionHandler(UncaughtExceptionHandler eh)

예제는 다음과 같다. 디폴트 handler 대신 각 쓰레드마다 UncaughtExceptionHandler를 등록했다. 
ExceptionLeakingTask 클래스와 ThreadExceptionHandler는 동일하다. 여기서는 하나의 ExceptionLeakingTask 클래스를 구현했지만, 각기 다른 쓰레드마다 별도의 handler를 등록하여 처리할 수 있다는 것을 명심하자. 

```java
import java.lang.Thread.UncaughtExceptionHandler;

public class HandlingUncaughtExceptionsForThreads {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		//Thread.setDefaultUncaughtExceptionHandler(new ThreadExceptionHandler("DefaultHandler")); 

		Thread thread1 = new Thread(new ExceptionLeakingTask(), "Mythread-1");
		thread1.setUncaughtExceptionHandler(new ThreadExceptionHandler("Handler-1"));

		Thread thread2 = new Thread(new ExceptionLeakingTask(), "Mythread-2");
		thread2.setUncaughtExceptionHandler(new ThreadExceptionHandler("Handler-2"));
		
		thread1.start();
		thread2.start();
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}

class ThreadExceptionHandler implements UncaughtExceptionHandler{
	private String handlerName;
	
	public ThreadExceptionHandler(String handlerName) {
		this.handlerName = handlerName;
	}

	@Override
	public void uncaughtException(Thread thread, Throwable e) {
		System.out.println(handlerName + " caught Exception in Thread - " 
			+ thread.getName()
			+ " => " + e);
	}

}
```

결과는 다음과 같다. 각기 다른 handler 객체가 정상적으로 Exception이 처리되는 것을 볼 수 있다.

```bash
[main] thread starts here...
[main] thread ends here...
Handler-2 caught Exception in Thread - Mythread-2 => java.lang.RuntimeException
Handler-1 caught Exception in Thread - Mythread-1 => java.lang.RuntimeException
```

#### 1.2.3 디폴트 handler와 쓰레드 별 handler를 조합해서 사용하기

위에서 살펴본 디폴트 handler와 쓰레드 별로 각기 다른 handler를 등록하는 것을 조합해서 사용해 보자. 이것은 다음과 같다. 

1. 디폴트 UncaughtException handler를 등록
2. 디폴트 handler를 오버라이드해서 쓰레드별 handler를 등록하기 

예제를 보면 쉽게 이해 할 수 있다. 아래 예제에서는 thread1은 DefaultHandler를 이용하고, thread2는 Handler-2를 이용한다. 

```java
import java.lang.Thread.UncaughtExceptionHandler;

public class HandlingUncaughtExceptionsForThreads {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread.setDefaultUncaughtExceptionHandler(new ThreadExceptionHandler("DefaultHandler")); 

		Thread thread1 = new Thread(new ExceptionLeakingTask(), "Mythread-1");
		//thread1.setUncaughtExceptionHandler(new ThreadExceptionHandler("Handler-1"));

		Thread thread2 = new Thread(new ExceptionLeakingTask(), "Mythread-2");
		thread2.setUncaughtExceptionHandler(new ThreadExceptionHandler("Handler-2"));
		
		thread1.start();
		thread2.start();
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}

class ThreadExceptionHandler implements UncaughtExceptionHandler{
	private String handlerName;
	
	public ThreadExceptionHandler(String handlerName) {
		this.handlerName = handlerName;
	}

	@Override
	public void uncaughtException(Thread thread, Throwable e) {
		System.out.println(handlerName + " caught Exception in Thread - " 
			+ thread.getName()
			+ " => " + e);
	}

}
```

결과는 다음과 같다. thread1은 DefaultHandler를 이용하고, thread2는 Handler-2를 이용하여 Exception을 처리하는 것을 알 수 있다. 

```bash
[main] thread starts here...
[main] thread ends here...
DefaultHandler caught Exception in Thread - Mythread-1 => java.lang.RuntimeException
Handler-2 caught Exception in Thread - Mythread-2 => java.lang.RuntimeException
```

## 2 Executor에서의 UncaughtException 처리하기 

일단 Executor에서의 UncaughtException 처리는 일반 쓰레드에서의 처리와 크게 다르지 않음을 명심하자. 
일반 쓰레드의 경우와 동일하게 UncaughtExceptionHandler 인터페이스를 구현하자. 
UncaughtExceptionHandler 인터페이스는 다음의 메소드 하나만을 가진다. 

> void uncaughtException(Thread thread, Throwable e)

UncaughtExceptionHandler를 이용하는 방법은 3가지가 있다. 

#### 2.2.1 시스템 내의 모든 쓰레드를 위한 디폴트 handler 지정하기 

어떻게 사용하는지는 바로 예제를 통해 알아보자. 

```java
import java.lang.Thread.UncaughtExceptionHandler;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadFactory;

public class HandlingUncaughtExceptionsForExecutors {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread.setDefaultUncaughtExceptionHandler(new ThreadExceptionHandler("Default Handler"));

		ExecutorService execService1 = Executors.newCachedThreadPool();
		execService1.execute(new ExceptionLeakingTask());
		execService1.execute(new ExceptionLeakingTask());

		ExecutorService execService2 = Executors.newCachedThreadPool();
		execService2.execute(new ExceptionLeakingTask());
		execService2.execute(new ExceptionLeakingTask());
		
	
		execService1.shutdown();
		execService2.shutdown();
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}

class ThreadExceptionHandler implements UncaughtExceptionHandler{
	private String handlerName;
	
	public ThreadExceptionHandler(){
		handlerName = "This";
	}

	public ThreadExceptionHandler(String handlerName) {
		this.handlerName = handlerName;
	}

	@Override
	public void uncaughtException(Thread thread, Throwable e) {
		System.out.println(handlerName + " caught Exception in Thread - " 
			+ thread.getName()
			+ " => " + e);
	}

}
```

결과는 다음과 같다. 시스템 전부에 걸쳐 디폴트 handler가 잘 처리하고 있음을 알 수 있다. 

```bash
[main] thread starts here...
Default Handler caught Exception in Thread - pool-1-thread-2 => java.lang.RuntimeException
Default Handler caught Exception in Thread - pool-1-thread-1 => java.lang.RuntimeException
Default Handler caught Exception in Thread - pool-2-thread-1 => java.lang.RuntimeException
Default Handler caught Exception in Thread - pool-2-thread-2 => java.lang.RuntimeException
[main] thread ends here...
```

#### 2.2.2 쓰레드 별로 handler를 각기 지정하기  

ThreadFactory를 구현하고 아래 setUncaughtExceptionHandler()를 사용하면 된다. 

> void Thread.setUncaughtExceptionHandler(UncaughtExceptionHandler eh)

예제는 다음과 같다. ThreadFactory를 이용해 디폴트 handler 대신 각 쓰레드마다 UncaughtExceptionHandler를 등록했다. 
ThreadFactory는 이전에 사용한 Thread마다 이름을 부여하는데 사용한 NamedThreadsFactory를 상속받았다. 


```java
import java.lang.Thread.UncaughtExceptionHandler;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadFactory;

public class HandlingUncaughtExceptionsForExecutors {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		//Thread.setDefaultUncaughtExceptionHandler(new ThreadExceptionHandler("Default Handler"));

		//ExecutorService execService1 = Executors.newCachedThreadPool();
		ExecutorService execService1 = Executors.newCachedThreadPool(new ThreadFactoryWithExceptionHandler());
		execService1.execute(new ExceptionLeakingTask());
		execService1.execute(new ExceptionLeakingTask());

		//ExecutorService execService2 = Executors.newCachedThreadPool();
		ExecutorService execService2 = Executors.newCachedThreadPool(new ThreadFactoryWithExceptionHandler());
		execService2.execute(new ExceptionLeakingTask());
		execService2.execute(new ExceptionLeakingTask());
		
	
		execService1.shutdown();
		execService2.shutdown();
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}

class ThreadExceptionHandler implements UncaughtExceptionHandler{
	private String handlerName;
	
	public ThreadExceptionHandler(){
		handlerName = "This";
	}

	public ThreadExceptionHandler(String handlerName) {
		this.handlerName = handlerName;
	}

	@Override
	public void uncaughtException(Thread thread, Throwable e) {
		System.out.println(handlerName + " caught Exception in Thread - " 
			+ thread.getName()
			+ " => " + e);
	}

}

class NamedThreadsFactory implements ThreadFactory {
	
	private static int count = 0;
	private static String Name = "MyThread-";

	@Override
	public Thread newThread(Runnable r) {
		return new Thread(r, Name + ++count);
	}
}

class ThreadFactoryWithExceptionHandler extends NamedThreadsFactory {
	public Thread newThread(Runnable r) {
		Thread thread = super.newThread(r);
		thread.setUncaughtExceptionHandler(new ThreadExceptionHandler());
		return thread;
	}
}
```

결과는 다음과 같다. 시스템 전부에 걸쳐 DefaultHandler가 잘 처리하고 있음을 알 수 있다. 

```bash
[main] thread starts here...
This caught Exception in Thread - MyThread-1 => java.lang.RuntimeException
This caught Exception in Thread - MyThread-2 => java.lang.RuntimeException
This caught Exception in Thread - MyThread-3 => java.lang.RuntimeException
This caught Exception in Thread - MyThread-6 => java.lang.RuntimeException
[main] thread ends here...
```

#### 2.2.3 디폴트 handler와 쓰레드 별 handler를 조합해서 사용하기

위에서 살펴본 디폴트 handler와 쓰레드 별로 각기 다른 handler를 등록하는 것을 조합해서 사용해 보자. 이것은 일반 쓰레드와 동일한 컨셉이다. 

1. 디폴트 UncaughtException handler를 등록
2. 디폴트 handler를 오버라이드해서 쓰레드별 handler를 등록하기 

컨셉이 동일하므로 예제를 통해 확인해보자. 아래는 ThreadFactory에서 쓰레드를 만들때 번갈아 가며 디폴트가 아닌 handler를 사용하도록 한 예제이다. 

```java
import java.lang.Thread.UncaughtExceptionHandler;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadFactory;

public class HandlingUncaughtExceptionsForExecutors {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread.setDefaultUncaughtExceptionHandler(new ThreadExceptionHandler("Default Handler"));

		//ExecutorService execService1 = Executors.newCachedThreadPool();
		ExecutorService execService1 = Executors.newCachedThreadPool(new ThreadFactoryWithExceptionHandlerAlternator());
		execService1.execute(new ExceptionLeakingTask());
		execService1.execute(new ExceptionLeakingTask());

		//ExecutorService execService2 = Executors.newCachedThreadPool();
		ExecutorService execService2 = Executors.newCachedThreadPool(new ThreadFactoryWithExceptionHandlerAlternator());
		execService2.execute(new ExceptionLeakingTask());
		execService2.execute(new ExceptionLeakingTask());
		
	
		execService1.shutdown();
		execService2.shutdown();
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class ExceptionLeakingTask implements Runnable {

	@Override
	public void run() {
		throw new RuntimeException();
	}

}

class ThreadExceptionHandler implements UncaughtExceptionHandler{
	private String handlerName;
	
	public ThreadExceptionHandler(){
		handlerName = "This";
	}

	public ThreadExceptionHandler(String handlerName) {
		this.handlerName = handlerName;
	}

	@Override
	public void uncaughtException(Thread thread, Throwable e) {
		System.out.println(handlerName + " caught Exception in Thread - " 
			+ thread.getName()
			+ " => " + e);
	}

}

class NamedThreadsFactory implements ThreadFactory {
	
	private static int count = 0;
	private static String Name = "MyThread-";

	@Override
	public Thread newThread(Runnable r) {
		return new Thread(r, Name + ++count);
	}
}

class ThreadFactoryWithExceptionHandlerAlternator extends NamedThreadsFactory {
	private static int count = 0;
	public Thread newThread(Runnable r) {
		Thread thread = super.newThread(r);
		if(count++ % 2 == 0) {	
			thread.setUncaughtExceptionHandler(new ThreadExceptionHandler());
		}
		return thread;
	}
}
```

결과는 다음과 같다.  

```bash
[main] thread starts here...
This caught Exception in Thread - MyThread-1 => java.lang.RuntimeException
This caught Exception in Thread - MyThread-3 => java.lang.RuntimeException
Default Handler caught Exception in Thread - MyThread-2 => java.lang.RuntimeException
Default Handler caught Exception in Thread - MyThread-6 => java.lang.RuntimeException
[main] thread ends here...
```
