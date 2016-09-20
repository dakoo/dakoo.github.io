---
layout: post
title: Java - (멀티쓰레딩 7) 쓰레드 중지하기 
description: Java - (멀티쓰레딩 7) 쓰레드 중지하기 
modified: 2016-09-19
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

쓰레드를 중지하는 방법을 살펴보자. 
지금까지와 같이 일반 쓰레드를 중지하는 방법과 Executor를 중지하는 법을 각각 살펴보자. 그리고 Executor의 경우 한번에 모든 태스크를 중지하는 법도 살펴보자. 
일반 쓰레드를 중지시키는 것과 Executor를 중지시키는 것의 차이는 다음과 같다. 

- 일반 쓰레드: 쓰레드를 종료한다. 
- Executor: 태스크를 종료한다. 모든 태스크가 종료되면 쓰레드는 자동으로 종료한다. 
 
## 1. 일반 쓰레드에서 쓰레드 중지 시키기 

일반 쓰레드를 종료하는 방법은 다음 3가지가 있다. 

- 플래그 사용하여 쓰레드 중지
- Non-blocking 태스크의 경우 인터럽트 발생 여부 확인하여 쓰레드 중지
- Blocking 태스크의 경우 인터럽트를 catch하여 쓰레드 중지

### 1.1 플래그 사용하여 쓰레드 중지

일반적인 쓰레드에서 loop을 도는 태스크를 종료하는 대표적인 방법은 플래그를 사용하는 것이다. Loop을 돌면서 종료 플래그를 확인하여 true이면 종료하는 방법이다. 쓰레드간의 동기화를 위해 volatile 키워드를 이용해 플래그 변수를 선언하고 synchronized를 이용해 atomic하게 만든다. 
예제는 다음과 같다. 

```java
import java.util.concurrent.TimeUnit;

public class TerminatingNormalThreadWithFlag {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		LoopTask task1 = new LoopTask();
		LoopTask task2 = new LoopTask();

		new Thread(task1, "Thread-1").start();
		new Thread(task2, "Thread-2").start();

		TimeUnit.MILLISECONDS.sleep(1000);
		task1.cancel();
		task2.cancel();

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private volatile boolean shutdown = false;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			try {
				TimeUnit.MILLISECONDS.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			synchronized(this) {
				if(shutdown) {
					break;
				}
			}
		}		
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}
	
	public void cancel() {
		System.out.println("... <" + Thread.currentThread().getName() +"," + taskId + "> shutting down...");
		synchronized (this) {
			this.shutdown = true;
		}
	}
	
	public LoopTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
}
```

결과는 다음과 같다. 

```bash
[main] thread starts here...
#### <Thread-1,Task-1> starting...####
#### <Thread-2,Task-2> starting...####
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
... <main,Task-1> shutting down...
... <main,Task-2> shutting down...
[main] thread ends here...
#### <Thread-2,Task-2> done...####
#### <Thread-1,Task-1> done...####
```

### 1.2 Non-blocking 태스크의 경우 인터럽트 발생 여부 확인하여 쓰레드 중지

블록되지 않고 동작하는 쓰레드를 중지시키는 좋은 방법은 인터럽트를 사용하여 종료하는 것이다. 인터럽트를 제공하는 Thread API는 다음과 같다. 

- void Thread.interrupt(): 쓰레드에 인터럽트를 호출
- static boolean Thread.interrupted() : 쓰레드 내부에서 쓰레드가 인터럽트 되었는지 확인하기 위해 사용
- boolean Thread.isInterrupted(): 다른 쓰레드에서 호출하여 쓰레드가 인터럽트 되었는지 확인

주의할 점은 **blocking 함수(타이머 등)와 함께 사용하면 Exception이 발생**한다는 것이다. 이는 컴파일 단계에서는 나타나지 않는다. 

아래 예는 인터럽트를 사용해 쓰레드를 중지시키는 예제인데 타이머를 사용하지 않는 Loop을 만들기 위해 doSomeWork()를 만들었다. 

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Random;
import java.util.concurrent.TimeUnit;

public class TerminatingNormalNonBlockingThreadWithInterrupt {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();

		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		LoopTask task1 = new LoopTask();
		LoopTask task2 = new LoopTask();

		Thread thread1 = new Thread(task1, "Thread-1");
		Thread thread2 = new Thread(task2, "Thread-2");
		thread1.start();
		thread2.start();

		TimeUnit.MILLISECONDS.sleep(500);
		
		thread1.interrupt();
		thread2.interrupt();

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private final int DATA_SIZE = 100000;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			doSomeWork(); //Interrupt can not work with timer because the timer is a blocking function
			if(Thread.interrupted()){  //Thread.interrupted()
				break;
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public LoopTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
	private void doSomeWork(){
		for(int i = 0; i<2 ; i++) {
			Collections.sort(generateDataSet());
		}
	}
	private List<Integer> generateDataSet(){
		List<Integer> intList = new ArrayList<>();
		Random randomGenerator = new Random();
		for(int i = 0; i<DATA_SIZE; i++) {
			intList.add(randomGenerator.nextInt(DATA_SIZE));
		}
		return intList;
	}
}
```

결과는 아래와 같이 잘 동작함을 알 수 있다. 

```bash
[main] thread starts here...
#### <Thread-1,Task-1> starting...####
#### <Thread-2,Task-2> starting...####
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
[main] thread ends here...
#### <Thread-1,Task-1> done...####
#### <Thread-2,Task-2> done...####
```

### 1.3 Blocking 태스크의 경우 인터럽트를 catch하여 쓰레드 중지

블록킹 쓰레드에서는 어떻게 종료를 시킬까? 그 방법은 위 방법을 응용하면 된다. 즉, 인터럽트를 이용해 중지를 시키면 된다. 
인터럽트를 외부 쓰레드에서 발생시키면 Sleep과 같은 Blocking함수에서 **Interrupted Exception**이 발생하는데 이때 그 **Exception catch 블록에서 loop을 중지**시키는 것이다. 

```java
import java.util.concurrent.TimeUnit;

public class TerminatingNormalBlockingThreadWithInterrupt {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();

		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		LoopTask task1 = new LoopTask();
		LoopTask task2 = new LoopTask();

		Thread thread1 = new Thread(task1, "Thread-1");
		Thread thread2 = new Thread(task2, "Thread-2");
		thread1.start();
		thread2.start();

		TimeUnit.MILLISECONDS.sleep(500);
		
		thread1.interrupt();
		thread2.interrupt();

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			try {
				TimeUnit.MILLISECONDS.sleep(100);
			} catch (InterruptedException e) {
				System.out.println("<" + currentThreadName + "," + taskId + "> Sleep Interrupted. Cancelling...");
				break;
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public LoopTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
}
```

결과는 아래와 같이 Interrupted Exception에 의해 종료됨을 확인 할 수 있다. 

```bash
[main] thread starts here...
#### <Thread-1,Task-1> starting...####
#### <Thread-2,Task-2> starting...####
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
[main] thread ends here...
<Thread-1,Task-1> Sleep Interrupted. Cancelling...
#### <Thread-1,Task-1> done...####
<Thread-2,Task-2> Sleep Interrupted. Cancelling...
#### <Thread-2,Task-2> done...####
```


## 2. 개개의 Executor 태스크를 중지 시키기 

Executor에서는 쓰레드를 중지하는 개념이 아니라 태스크를 중지시킨다. 그 외에는 일반 쓰레드에서의 중지 방법과 대부분 유사하다. 

- 플래그 사용하여 태스크 중지
- Non-blocking 태스크의 경우 인터럽트 발생 여부 확인하여 중지
- Blocking 태스크의 경우 인터럽트를 catch하여 중지

### 2.1 플래그 사용하여 태스크 중지

일반적인 쓰레드에서 loop을 도는 태스크를 종료하는 대표적인 방법은 플래그를 사용하는 것이다. Loop을 돌면서 종료 플래그를 확인하여 true이면 종료하는 방법이다. 쓰레드간의 동기화를 위해 volatile 키워드를 이용해 플래그 변수를 선언하고 synchronized를 이용해 atomic하게 만든다. 
예제는 다음과 같다. 

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class TerminatingExecutorWithFlag {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newCachedThreadPool();
		
		LoopTask task1 = new LoopTask();
		execService.execute(task1);
		
		execService.shutdown();

		TimeUnit.MILLISECONDS.sleep(1000);

		task1.cancel();

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private volatile boolean shutdown = false;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			try {
				TimeUnit.MILLISECONDS.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			synchronized(this) {
				if(shutdown) {
					break;
				}
			}
		}		
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}
	
	public void cancel() {
		System.out.println("... <" + Thread.currentThread().getName() +"," + taskId + "> shutting down...");
		synchronized (this) {
			this.shutdown = true;
		}
	}
	
	public LoopTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
}
```

결과는 다음과 같다. 

```bash
[main] thread starts here...
#### <pool-1-thread-1,Task-1> starting...####
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
... <main,Task-1> shutting down...
[main] thread ends here...
#### <pool-1-thread-1,Task-1> done...####

```

### 2.2 Non-blocking 태스크의 경우 인터럽트 발생 여부 확인하여 중지

Executor에서 블록되지 않고 동작하는 태스크를 중지시키는 좋은 방법은 인터럽트를 사용하여 종료하는 것이다. Executor에서 인터럽트를 제공하는 Thread API는 다음과 같다. 

- boolean Future.cancel(boolean mayInterruptIfRunning): Executor의 태스크 멈추기. 만약 태스크가 이미 다른 이유로 cancel되었다면 false를 반환. 일반 쓰레드에서의 Thread.interrupt()와 유사한 역할
- static boolean Thread.interrupted() :  태스크가 인터럽트 되었는지 확인하기 위해 사용. 일반 쓰레드와 동일
- boolean Future.isCanceled(): 다른 쓰레드에서 호출. boolean Thread.isInterrupted()와 유사 

중요한 것은 위의 API는 쓰레드가 아닌 **태스크**를 중지시키는 것이라는 것이다. 아래 예를 보면 하나의 쓰레드만 동작하면서 순차적으로 태스크를 종료시키는 예이다. 
쓰레드를 점유한 첫번째 태스크를 종료시키면 두번째 태스크가 동작하고 두번째 태스크를 종료시키면 더이상 태스크가 없으므로 쓰레드가 멈추게 된다. 

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Random;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

public class TerminatingNonBlockingExecutorWithInterrupt {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();

		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newSingleThreadExecutor();
		
		LoopTask task1 = new LoopTask();
		LoopTask task2 = new LoopTask();

		Future<?> future1 = execService.submit(task1);
		Future<?> future2 = execService.submit(task2);
		
		execService.shutdown();

		TimeUnit.MILLISECONDS.sleep(200);
		future1.cancel(true);
		TimeUnit.MILLISECONDS.sleep(100);
		future2.cancel(true);

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private final int DATA_SIZE = 100000;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			doSomeWork(); 
			if(Thread.interrupted()){ 
				System.out.println("<" + currentThreadName + "," + taskId + "> got an interrupt! ..canceling...");
				break;
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public LoopTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
	private void doSomeWork(){
		for(int i = 0; i<2 ; i++) {
			Collections.sort(generateDataSet());
		}
	}
	private List<Integer> generateDataSet(){
		List<Integer> intList = new ArrayList<>();
		Random randomGenerator = new Random();
		for(int i = 0; i<DATA_SIZE; i++) {
			intList.add(randomGenerator.nextInt(DATA_SIZE));
		}
		return intList;
	}
}
```

결과는 아래와 같이 첫번째 태스크가 멈춘 후 같은 쓰레드에서 두번째 쓰레드가 동작한다. 

```bash
[main] thread starts here...
[main] thread starts here...
#### <pool-1-thread-1,Task-1> starting...####
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> got an interrupt! ..canceling...
#### <pool-1-thread-1,Task-1> done...####
#### <pool-1-thread-1,Task-2> starting...####
<pool-1-thread-1,Task-2> TICK TICK
<pool-1-thread-1,Task-2> TICK TICK
<pool-1-thread-1,Task-2> TICK TICK
[main] thread ends here...
<pool-1-thread-1,Task-2> got an interrupt! ..canceling...
#### <pool-1-thread-1,Task-2> done...####

```

### 2.3 Blocking 태스크의 경우 인터럽트를 catch하여 중지

블록킹 태스크를 중지시키는 것도 일반 쓰레드의 경우와 동일하다. 외부 쓰레드에서 cancel()을 호추하면 Sleep과 같은 Blocking함수에서 **Interrupted Exception**이 발생하는데 이때 그 **Exception catch 블록에서 loop을 중지**시키는 것이다. 
아래의 예도 하나의 쓰레드에서 두개의 태스크를 submit한 경우로 InterruptedException을 이용해 순차적으로 중지시킨다. 


```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

public class TerminatingBlockingExecutorWithInterrupt {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();

		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newSingleThreadExecutor();
		
		LoopTask task1 = new LoopTask();
		LoopTask task2 = new LoopTask();

		Future<?> future1 = execService.submit(task1);
		Future<?> future2 = execService.submit(task2);
		
		execService.shutdown();

		TimeUnit.MILLISECONDS.sleep(200);
		future1.cancel(true);
		TimeUnit.MILLISECONDS.sleep(100);
		future2.cancel(true);

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			try {
				TimeUnit.MILLISECONDS.sleep(100);
			} catch (InterruptedException e) {
				System.out.println("<" + currentThreadName + "," + taskId + "> Sleep Interrupted. Cancelling...");
				break;
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public LoopTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
}
```

결과는 아래와 같이 Interrupted Exception에 의해 종료됨을 확인 할 수 있다. 

```bash
[main] thread starts here...
[main] thread starts here...
#### <pool-1-thread-1,Task-1> starting...####
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> Sleep Interrupted. Cancelling...
#### <pool-1-thread-1,Task-1> done...####
#### <pool-1-thread-1,Task-2> starting...####
<pool-1-thread-1,Task-2> TICK TICK
<pool-1-thread-1,Task-2> TICK TICK
[main] thread ends here...
<pool-1-thread-1,Task-2> Sleep Interrupted. Cancelling...
#### <pool-1-thread-1,Task-2> done...####
```

## 3. 모든 Executor를 한번에 중지시키기

ExecutorService는 모든 태스크를 한번에 중지시키는 shutdownNow() 메소드를 제공하고 있다. 

> List<Runnable> ExecutorService.shutdownNow() 

- 내부적으로는 위에서 살펴본 인터럽트를 사용하는 방식이다. 
- 반환되는 List는 아직 종료되지 않은 태스크들의 목록이다. 
- 만약 모든 태스크들이 종료가 되었다면 ExecutorService도 종료한다. 
- Non-blocking 태스크는 물론이고 Blocking 태스크 또한 종료 시키는데 위에서 살펴본 인터럽트를 사용하는 방식이므로 Exception을 정확히 다루어야 한다. 
- 만약 아직 실행이 시작되지 않은 태스크가 있다면 종료된 것으로 처리한다. 

shutdownNow()를 호출후에 태스크가 완전히 종료 될 때까지 기다리기 위해서는 awaitTermiation() 메소드를 사용한다.  

> boolean ExecService.awaitTermination(long timeout, TimeUnit unit)

- shutdown 이후 일정시간동안 block된다. timeout과 unit을 이용해 blocking되는 시간을 지정한다. 
- shutdown 이후 모든 태스크가 종료될때 까지 block된다. 
- 외부에서 실행되는 쓰레드에 인터럽트를 걸때까지 block된다. 
- 위의 3가지 경우 중 가장 빨리 조건에 해제되는 시점까지 block되며 이때 모든 태스크가 종료되었으면 true를 반환한다. 

예제는 다음과 같다. Blocking, Non-Blocking, Callable 태스크 모두를 한번에 종료하는 예제이다. 각각 모두 인터럽트를 잘 처리해야 한다. non-Blocking은 인터럽트 여부를 확인해야 하고, Blocking은 InterruptedException을 catch해야 한다. 

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Random;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

public class TerminatingAllExecutors {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newCachedThreadPool();
		
		BlockingTask blockingTask = new BlockingTask();
		NonBlockingTask nonBlockingTask = new NonBlockingTask();
		CallableTask callableTask = new CallableTask();
		
		//Runnable
		execService.execute(blockingTask);
		execService.execute(nonBlockingTask);
		//Callable
		execService.submit(callableTask);

		TimeUnit.MILLISECONDS.sleep(1000);
		
		execService.shutdownNow(); //shutDownNow
		System.out.println("["+ currentThreadName + "]" + " shutdownNow() invoked ");
		
		System.out.println("["+ currentThreadName + "]" + " All threads terminated: " +
				execService.awaitTermination(500, TimeUnit.MILLISECONDS)); //awaitTermination

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class BlockingTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			try {
				TimeUnit.MILLISECONDS.sleep(100);
			} catch (InterruptedException e) {
				System.out.println("<" + currentThreadName + "," + taskId + "> Sleep Interrupted. Cancelling...");
				break;
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public BlockingTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
}

class NonBlockingTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private final int DATA_SIZE = 100000;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			doSomeWork(); 
			if(Thread.interrupted()){ 
				System.out.println("<" + currentThreadName + "," + taskId + "> Thread.interrupted() is true: Cancelling...");
				break;
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public NonBlockingTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
	private void doSomeWork(){
		for(int i = 0; i<2 ; i++) {
			Collections.sort(generateDataSet());
		}
	}
	private List<Integer> generateDataSet(){
		List<Integer> intList = new ArrayList<>();
		Random randomGenerator = new Random();
		for(int i = 0; i<DATA_SIZE; i++) {
			intList.add(randomGenerator.nextInt(DATA_SIZE));
		}
		return intList;
	}
}

class CallableTask implements Callable<Integer> {
	private static int count = 0;
	private int id;
	private String taskId;
	private int randomSum = 0;

	@Override
	public Integer call() throws Exception {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		while(true) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			randomSum += Math.random()*1000;
			try {
				TimeUnit.MILLISECONDS.sleep(100);
			} catch (InterruptedException e) {
				System.out.println("<" + currentThreadName + "," + taskId + "> Sleep Interrupted. Cancelling...");
				break;
			}
		}		
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
		return randomSum;
	}
	public CallableTask() {
		this.id = ++count;
		this.taskId = "Task-" + id;
	}
}


```

결과는 아래와 같다. shutdownNow()이 호출된 후 각각 태스크는 종료된다. 

```bash
[main] thread starts here...
#### <pool-1-thread-1,Task-1> starting...####
#### <pool-1-thread-2,Task-1> starting...####
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-2,Task-1> TICK TICK
#### <pool-1-thread-3,Task-1> starting...####
<pool-1-thread-3,Task-1> TICK TICK
<pool-1-thread-2,Task-1> TICK TICK
<pool-1-thread-3,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-2,Task-1> TICK TICK
<pool-1-thread-2,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-3,Task-1> TICK TICK
[main] shutdownNow() invoked 
<pool-1-thread-1,Task-1> Sleep Interrupted. Cancelling...
<pool-1-thread-3,Task-1> Sleep Interrupted. Cancelling...
#### <pool-1-thread-3,Task-1> done...####
#### <pool-1-thread-1,Task-1> done...####
<pool-1-thread-2,Task-1> Thread.interrupted() is true: Cancelling...
#### <pool-1-thread-2,Task-1> done...####
[main] All threads terminated: true
[main] thread ends here...
```

다음과 같이 TerminatingAllExecutors 클래스를 수정해서 의도적으로 실행되지 않는 태스크를 만들어보자. 

```java
public class TerminatingAllExecutors {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newSingleThreadExecutor();
		
		BlockingTask blockingTask = new BlockingTask();
		NonBlockingTask nonBlockingTask = new NonBlockingTask();
		CallableTask callableTask = new CallableTask();
		
		//Runnable
		execService.execute(blockingTask);
		execService.execute(nonBlockingTask);
		//Callable
		execService.submit(callableTask);

		TimeUnit.MILLISECONDS.sleep(200);
		
		execService.shutdownNow(); //shutDownNow
		System.out.println("["+ currentThreadName + "]" + " shutdownNow() invoked ");
		
		System.out.println("["+ currentThreadName + "]" + " All threads terminated: " +
				execService.awaitTermination(500, TimeUnit.MILLISECONDS)); //awaitTermination

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}
```

아래 결과와 같이 실행되지 않은 태스크는 더이상 실행되지 않는다. 

```bash
[main] thread starts here...
#### <pool-1-thread-1,Task-1> starting...####
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
<pool-1-thread-1,Task-1> TICK TICK
[main] shutdownNow() invoked 
<pool-1-thread-1,Task-1> Sleep Interrupted. Cancelling...
#### <pool-1-thread-1,Task-1> done...####
[main] All threads terminated: true
[main] thread ends here...
```

