---
layout: post
title: Java - (멀티쓰레딩 5) 데몬 쓰레드 
modified: 2016-09-18
tags: [java]
parent: Java
nav_order: 2
---

## 데몬 쓰레드 

데몬쓰레드는 다른 User 스레드의 작업을 돕는 보조적인 역할을 수행하도록 되어 있다. 그래서 핵심적인 특징은 다른 User 쓰레드가 모두 종료되면 데몬 쓰레드는 강제적으로 종료되어진다. 
메인 쓰레드는 당연히도 User 스레드이다. 

### 1. 일반 쓰레드의 경우

일반 스레드에서 스레드를 데몬 스레드로 지정하는 방법은 setDaemon() 메소드이다. setDaemon(true)후에 start()를 시키면 된다. 

```java
import java.util.concurrent.TimeUnit;

public class DaemonThreads {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread t1 = new Thread(new MyRunnableTask(100), "Thread-1");
		Thread t2 = new Thread(new MyRunnableTask(500), "Thread-2");
		
		t2.setDaemon(true);
		
		t1.start();
		t2.start();
		
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
		boolean isRunningInDaemonThread = Thread.currentThread().isDaemon();
		String threadType = isRunningInDaemonThread ? "DAEMON" : "USER";
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +","+ threadType + "," + taskId + "> starting...####");
		for(int i = 0; i<10; i++) {
			System.out.println("<" + currentThreadName +","+ threadType + "," + taskId + "> TICK TICK" + i);
			try {
				TimeUnit.MILLISECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		System.out.println("#### <" + currentThreadName +","+ threadType + "," + taskId + "> done...####");
	}
	public MyRunnableTask(long sleepTime) {
		this.id = ++count;
		this.taskId = "MyRunnableTask" + id;
		this.sleepTime = sleepTime;
	}
}
```


위와 같이 빨리 수행되는 t1과 늦게 수행되는 t2를 구동하면서 t2를 데몬스레드로 지정하면 결과가 다음과 같이 된다. 
Main thread와 t1이 종료하면 t2는 더이상 진행하지 않고 즉시 종료된다. 

```bash
[main] thread starts here...
[main] thread ends here...
#### <Thread-1,USER,MyRunnableTask1> starting...####
#### <Thread-2,DAEMON,MyRunnableTask2> starting...####
<Thread-2,DAEMON,MyRunnableTask2> TICK TICK0
<Thread-1,USER,MyRunnableTask1> TICK TICK0
<Thread-1,USER,MyRunnableTask1> TICK TICK1
<Thread-1,USER,MyRunnableTask1> TICK TICK2
<Thread-1,USER,MyRunnableTask1> TICK TICK3
<Thread-1,USER,MyRunnableTask1> TICK TICK4
<Thread-2,DAEMON,MyRunnableTask2> TICK TICK1
<Thread-1,USER,MyRunnableTask1> TICK TICK5
<Thread-1,USER,MyRunnableTask1> TICK TICK6
<Thread-1,USER,MyRunnableTask1> TICK TICK7
<Thread-1,USER,MyRunnableTask1> TICK TICK8
<Thread-1,USER,MyRunnableTask1> TICK TICK9
<Thread-2,DAEMON,MyRunnableTask2> TICK TICK2
#### <Thread-1,USER,MyRunnableTask1> done...####

```

### 2. Executor의 경우 

Executor의 경우도 setDaemon()을 동일하게 사용한다. 단지 언제 호출을 하느냐가 문제인데 이것은 이전에 살펴본 ThreadFactory 인터페이스를 통해 구현한다. 
이전에 쓰레드의 이름을 주기 위해 NamedThreadsFactory 클래스를 구현했는데 이것을 상속한 DaemoneThreadsFactory를 이용해 쓰레드을 데몬으로 설정하는 것을 알아보자. 


```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.TimeUnit;


public class DaemonThreadsUsingExecutors {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService = Executors.newCachedThreadPool( new DaemoneThreadsFactory()); 

		execService.execute(new MyRunnableTask(100));
		execService.execute(new MyRunnableTask(400));	
		
		execService.shutdown();
		
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

class DaemoneThreadsFactory extends NamedThreadsFactory {

	private static int count = 0;

	@Override
	public Thread newThread(Runnable r) {
		Thread t = super.newThread(r);
		count++;
		if(count%2 == 0){
			t.setDaemon(true); //setDaemon()
		}
		return t;
	}
}
```


짝수 쓰레드를 데몬으로 만들었는데, 이 경우도 역시 Main 쓰레드와 User 쓰레드가 종료되면 데몬 쓰레드는 더이상 진행하지 않고 즉시 종료된다. 

```bash
[main] thread starts here...
#### <MyThread-1,USER,MyRunnableTask1> starting...####
#### <MyThread-2,DAEMON,MyRunnableTask2> starting...####
<MyThread-1,USER,MyRunnableTask1> TICK TICK0
<MyThread-2,DAEMON,MyRunnableTask2> TICK TICK0
[main] thread ends here...
<MyThread-1,USER,MyRunnableTask1> TICK TICK1
<MyThread-1,USER,MyRunnableTask1> TICK TICK2
<MyThread-1,USER,MyRunnableTask1> TICK TICK3
<MyThread-2,DAEMON,MyRunnableTask2> TICK TICK1
<MyThread-1,USER,MyRunnableTask1> TICK TICK4
<MyThread-1,USER,MyRunnableTask1> TICK TICK5
<MyThread-1,USER,MyRunnableTask1> TICK TICK6
<MyThread-1,USER,MyRunnableTask1> TICK TICK7
<MyThread-2,DAEMON,MyRunnableTask2> TICK TICK2
<MyThread-1,USER,MyRunnableTask1> TICK TICK8
<MyThread-1,USER,MyRunnableTask1> TICK TICK9
#### <MyThread-1,USER,MyRunnableTask1> done...####
```
