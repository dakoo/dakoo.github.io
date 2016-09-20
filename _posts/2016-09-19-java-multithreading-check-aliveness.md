---
layout: post
title: Java - (멀티쓰레딩 6) 쓰레드 동작의 완료를 확인하기 
description: Java - (멀티쓰레딩 6) 쓰레드 동작의 완료를 확인하기 
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

쓰레드 동작의 완료를 확인한다는 것은 다음 2가지를 말한다. 

- 일반 쓰레드에서 쓰레드가 살아있는지 확인하기 
- Executor에서 태스크가 종료되었는지 확인하기 

사실 일반 쓰레드에서도 쓰레드가 살아있는지를 확인하는 것보다 태스크의 종료에 대해 관심이 더 많지만 쓰레드 API 수준에서는 제공하지 않는다. 

### 1. 일반 쓰레드에서 쓰레드가 살아있는지 확인하기 

일반 스레드에서 쓰레드가 살이있는지 확인하는 방법은 `Thread.isAlive()` 메소드를 사용하는 것이다. 예제로 확인하자. 

```java
import java.util.concurrent.TimeUnit;


public class ThreadAliveCheck {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread thread = new Thread(new MyRunnableTask(10), "Thread-1");
		
		boolean threadIsAlive = thread.isAlive();
		System.out.println("thread Aliveness before start: " + threadIsAlive);

		thread.start();
	
		for(int i = 0; i< 4; i++) {
			TimeUnit.MILLISECONDS.sleep(20);
			System.out.println("thread Aliveness : " + thread.isAlive());
		}
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class MyRunnableTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private long sleepTime;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		for(int i = 0; i<4; i++) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK" + i);
			try {
				TimeUnit.MILLISECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}
	public MyRunnableTask(long sleepTime) {
		this.id = ++count;
		this.taskId = "MyRunnableTask" + id;
		this.sleepTime = sleepTime;
	}
}
```

아래 결과와 같이 쓰레드가 동작하고 있는동안에만 isAlive()가 true를 반환함을 알 수 있다. 

```bash
[main] thread starts here...
thread Aliveness before start: false
#### <Thread-1,MyRunnableTask1> starting...####
<Thread-1,MyRunnableTask1> TICK TICK0
<Thread-1,MyRunnableTask1> TICK TICK1
thread Aliveness : true
<Thread-1,MyRunnableTask1> TICK TICK2
<Thread-1,MyRunnableTask1> TICK TICK3
thread Aliveness : true
#### <Thread-1,MyRunnableTask1> done...####
thread Aliveness : false
thread Aliveness : false
[main] thread ends here...
```

### 2. Executor에서 태스크가 종료되었는지 확인하기 

태스크의 종료는 반드시 태스크가 정상적으로 종료함만을 의미하지 않는다. Exception으로 종료되는 것도 포함해서 종료되었는지 확인하는 것은 `Future.isDone()`함수이다. 
예제는 다음과 같다. 

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.TimeUnit;


public class ExecutorTaskCompleteCheck {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newCachedThreadPool( new NamedThreadsFactory()); 

		Future<?> future = execService.submit(new MyRunnableTask(10));
		
		execService.shutdown();
	
		for(int i = 0; i< 4; i++) {
			TimeUnit.MILLISECONDS.sleep(20);
			System.out.println("Task completion : " + future.isDone());
		}
		
		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
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

class MyRunnableTask implements Runnable {
	private static int count = 0;
	private int id;
	private String taskId;
	private long sleepTime;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		for(int i = 0; i<4; i++) {
			System.out.println("<" + currentThreadName +"," + taskId + "> TICK TICK" + i);
			try {
				TimeUnit.MILLISECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}
	public MyRunnableTask(long sleepTime) {
		this.id = ++count;
		this.taskId = "MyRunnableTask" + id;
		this.sleepTime = sleepTime;
	}
}
```

결과는 다음과 같다. 

```bash
[main] thread starts here...
#### <MyThread-1,MyRunnableTask1> starting...####
<MyThread-1,MyRunnableTask1> TICK TICK0
<MyThread-1,MyRunnableTask1> TICK TICK1
<MyThread-1,MyRunnableTask1> TICK TICK2
Task completion : false
<MyThread-1,MyRunnableTask1> TICK TICK3
#### <MyThread-1,MyRunnableTask1> done...####
Task completion : true
Task completion : true
Task completion : true
[main] thread ends here...
```

참고로 Future<T> future = ExecutorService.submit(new Callable()) 대신 FutureTask<T> future = new FutureTask<T>(new Callable())로 사용할 수 있다. 
