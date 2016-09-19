---
layout: post
title: Java - (멀티쓰레딩 7) 일반 쓰레드 종료하기 
description: Java - (멀티쓰레딩 7) 일반 쓰레드 종료하기 
modified: 2016-09-18
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

쓰레드 종료하는 방법을 살펴보자. 지금까지와 같이 일반 쓰레드를 종료하는 방법과 Executor를 종료하는 법을 각각 살펴보자. 

### 1. 일반적인 쓰레드에서 플래그를 이용해 중지시키기 

일반적인 쓰레드에서 loop을 도는 태스크를 종료하는 대표적인 방법은 플래그를 사용하는 것이다. Loop을 돌면서 종료 플래그를 확인하여 true이면 종료하는 방법이다. 쓰레드간의 동기화를 위해 volatile 키워드를 이용해 플래그 변수를 선언하고 synchronized를 이용해 atomic하게 만든다. 
예제는 다음과 같다. 

```java
package tutorial1;

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

### 2. non-blocking 쓰레드에서 인터럽트를 이용해 중지시키기

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

### 3. blocking 쓰레드에서 인터럽트를 이용해 중지시키기

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

