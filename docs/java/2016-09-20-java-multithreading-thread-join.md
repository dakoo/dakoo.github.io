---
layout: post
title: Java - (멀티쓰레딩 9) 쓰레드 Join하기
modified: 2016-09-19
tags: [java]
parent: Java
nav_order: 2
---

## 1 일반 쓰레드가 다른 쓰레드가 끝날때까지 대기하게 하기 

#### 1.1 이전에 살펴본 방식

쓰레드가 다른 쓰레드가 종료될 때까지 대기하는 방법은 이미 두 가지를 다루었다. 

- [(멀티쓰레딩 6) 쓰레드 동작의 완료를 확인하기](http://hochulshin.com/java-multithreading-check-aliveness/)에서 다룬 `Thread.isAlive()`를 사용하여 계속 확인을 하는 것이다. 
- [(멀티쓰레딩 4) 쓰레드에서 값 반환](http://hochulshin.com/java-multithreading-returning-values-from-task/)에서 다룬 `synchronized`에서 wait()와 notify()를 사용하는 블록킹 방식이다. 

첫번째 폴링하는 방법은 CPU의 낭비가 심하고 두번째 블록킹 방법은 제대로 구현하기 어렵다. 

### 1.2 join()을 사용해서 다른 쓰레드 종료를 기다리기

세번째 방법이 `Thread.join()` 메소드를 사용하는 것이다. 

> void Thread.join()

이 메소드는 wait()와 notify()처럼 동작을 하지만 사용하기가 상대적으로 편하다. 이미 대기하는 쓰레드가 종류한 경우에는 join()을 호출함과 동시에 리턴된다. 

예제는 아래와 같다. thread1, 2, 3가 실행이 되는데 thread3이 가장 먼저 끝나고, thread1이 다음, 마지막으로 thread2가 종료되도록 되어 있다. 
하지만, join()을 호출하는 순서가 thread1, 2, 3이므로 main thread는 thread1을 join한 뒤에야 thread2를 join하고 마지막으로 thread3를 join한다. 

```java
import java.util.concurrent.TimeUnit;

public class JoiningThreads {
	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		Thread thread1 = new Thread(new LoopTask(200), "Thread-1");
		Thread thread2 = new Thread(new LoopTask(500), "Thread-2");
		Thread thread3 = new Thread(new LoopTask(100), "Thread-3");

		thread1.start();
		thread2.start();
		thread3.start();
		
		thread1.join();
		System.out.println("["+ currentThreadName + "]" + " joined " + thread1.getName());
		
		thread2.join();
		System.out.println("["+ currentThreadName + "]" + " joined " + thread2.getName());
		
		thread3.join();
		System.out.println("["+ currentThreadName + "]" + " joined " + thread3.getName());

		System.out.println("["+ currentThreadName + "]" + " thread ends here...");
	}
}

class LoopTask implements Runnable {
	private static int count = 0;
	private int id;
	private long sleepTime;
	private String taskId;

	@Override
	public void run() { 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		for(int i = 0; i<4; i++) {
			System.out.println("<" + currentThreadName + "," + taskId + "> TICK TICK");
			try {
				TimeUnit.MILLISECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		System.out.println("#### <" + currentThreadName + "," + taskId + "> done...####");
	}

	public LoopTask(long sleepTime) {
		this.id = ++count;
		this.taskId = "Task-" + id;
		this.sleepTime = sleepTime;
	}
}
```

결과도 예상한 바와 마찬가지이다. thread내부에서 done이 출력한 순서는 3, 1, 2이지만, join이 출력된 순서는 1, 2, 3이다. 

```
[main] thread starts here...
#### <Thread-1,Task-1> starting...####
<Thread-1,Task-1> TICK TICK
#### <Thread-3,Task-3> starting...####
#### <Thread-2,Task-2> starting...####
<Thread-2,Task-2> TICK TICK
<Thread-3,Task-3> TICK TICK
<Thread-3,Task-3> TICK TICK
<Thread-1,Task-1> TICK TICK
<Thread-3,Task-3> TICK TICK
<Thread-3,Task-3> TICK TICK
<Thread-1,Task-1> TICK TICK
#### <Thread-3,Task-3> done...####
<Thread-2,Task-2> TICK TICK
<Thread-1,Task-1> TICK TICK
#### <Thread-1,Task-1> done...####
[main] joined Thread-1
<Thread-2,Task-2> TICK TICK
<Thread-2,Task-2> TICK TICK
#### <Thread-2,Task-2> done...####
[main] joined Thread-2
[main] joined Thread-3
[main] thread ends here...
```

### 1.3 join()을 사용해서 일반 쓰레드에서 값 반환하기

일반 쓰레드에서 값을 반환하는 방법은 이미 [(멀티쓰레딩 4) 쓰레드에서 값 반환](http://hochulshin.com/java-multithreading-returning-values-from-task/)에서 다루었다. 
그 글에서는 `synchronized`의 wait()와 notify()를 사용했다. 그런데 위에서 언급한 바와 같이 join()은 마치 wait()와 notify()처럼 동작한다. 
이런 join()의 동작을 이용하여 일반 쓰레드에서 값을 반환하는 것을 구현해 보자. 
[(멀티쓰레딩 4) 쓰레드에서 값 반환](http://hochulshin.com/java-multithreading-returning-values-from-task/)에서는 boolean 변수인 done과 synchronized {}를 사용했는데 join을 사용하면 그럴 필요가 없다. 
단지, join()을 한 후에 get() 메소드를 이용해 결과값을 읽어오면 되는 것이다. 그것은 쓰레드는 종료되었지만, 태스크 객체는 남아있기 때문에 가능한 것이다. 
[(멀티쓰레딩 4) 쓰레드에서 값 반환](http://hochulshin.com/java-multithreading-returning-values-from-task/)에서 사용한 예제를 수정한 예제는 다음과 같다. 

```java
public class ReturningValueFromThreads {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");

		MyThreadTask task1 = new MyThreadTask();
		MyThreadTask task2 = new MyThreadTask();
		MyThreadTask task3 = new MyThreadTask();

		Thread thread1 = new Thread(task1,"firstThread");
		Thread thread2 = new Thread(task2,"secondThread");
		Thread thread3 = new Thread(task3,"thirdThread");
		thread1.start();
		thread2.start();
		thread3.start();
		
		thread1.join();
		System.out.println("task1 result:" + task1.getRandomSum());
		thread2.join();
		System.out.println("task2 result:" + task2.getRandomSum());
		thread3.join();
		System.out.println("task3 result:" + task3.getRandomSum());
		
		System.out.println("Main thread ends here...");
	}
}


class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	private int randomSum = 0;

	@Override
	public void run(){
		for(int i = 0; i<5; i++) {
			System.out.println("<" + id + ">TICK TICK " + i);
			randomSum += Math.random()*1000;
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
	
	public int getRandomSum(){
		return randomSum;
	}

	public MyThreadTask() {
		this.id = ++count;
	}
}
```

결과도 예상한 바와 마찬가지이다.

```
Main thread starts here...
<1>TICK TICK 0
<2>TICK TICK 0
<3>TICK TICK 0
<2>TICK TICK 1
<3>TICK TICK 1
<1>TICK TICK 1
<2>TICK TICK 2
<1>TICK TICK 2
<3>TICK TICK 2
<2>TICK TICK 3
<1>TICK TICK 3
<3>TICK TICK 3
<2>TICK TICK 4
<1>TICK TICK 4
<3>TICK TICK 4
task1 result:3033
task2 result:2059
task3 result:3800
Main thread ends here...
```

## 2 Executor 태스크가 종료 될때까지 대기하기 

다른 Executor 태스크가 끝날때까지 한 태스크를 정지해 놓는 방법을 알아보자. 
Java는 이를 위해 `java.util.concurrent.CountDownLatch 클래스`를 제공한다. 일반 쓰레드는 Thread.join()하나를 사용할 뿐이지만, Executor는 클래스를 사용해야 한다. 
CountDownLatch object는 종료할 태스크들과 그 태스크들이 종료하길 기다리는 태스크들이 모두 공유한다. 몇 개의 태스크들이 종료해야 하는 지를 0보다 큰 숫자로 정해놓고 하나씩 줄여나가면서 기다리는 방식이다. 

- 대기하는 태스크: `void await()` 메소드를 호출하여 count가 0이 될때까지 블록되어 기다린다. 
- 종료하는 태스크: `void coutndown()` 메소드를 실행이 끝난후에 호출한다. 이러면 count가 1 감소한다. 

count가 0이 되면 대기하던 태스크들은 모두 블록킹 상태에서 풀려나서 실행을 지속하게 된다. 


```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.TimeUnit;

public class JoiningExecutors {
	public static void main(String argc[]) {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("["+ currentThreadName + "]" + " thread starts here...");

		ExecutorService execService1 = Executors.newCachedThreadPool(new NamedThreadsFactory());
		CountDownLatch doneSignal = new CountDownLatch(4); //set the initial count

		execService1.execute(new LoopTask(doneSignal));
		execService1.execute(new LoopTask(doneSignal));
		execService1.execute(new LoopTask(doneSignal));
		execService1.execute(new LoopTask(doneSignal));

		execService1.shutdown();
		
		try {
			doneSignal.await(); //wait for the count = 0
			System.out.println("["+ currentThreadName + "]" + " got the signal to continue...");
		} catch(InterruptedException e) {
			e.printStackTrace();
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

class LoopTask implements Runnable {	
	private static int count = 0;
	private int id;
	private String taskId;
	private CountDownLatch doneCountLatch;

	@Override
	public void run(){
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId + "> starting...####");
		for(int i = 0; i<5; i++) {
			System.out.println("<" + currentThreadName +"," + taskId + "> TICK TICK" + i);
			try {
				TimeUnit.MILLISECONDS.sleep((long)Math.random()*1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		System.out.println("#### <" + currentThreadName +"," + taskId + "> done...####");
		if(doneCountLatch != null) {
			doneCountLatch.countDown();	//count-- 
			System.out.println("#### <" + currentThreadName +"," + taskId + "> LATCH COUNT =" + doneCountLatch.getCount());
		}
	}
	
	public LoopTask(CountDownLatch doneCountLatch) {
		this.id = ++count;
		this.taskId = "LoopTask" + id;
		this.doneCountLatch = doneCountLatch;
	}
}
```

결과도 예상한 바와 마찬가지이다. COUNT가 0이 된다음에야 await()를 지나가서 got the signal to continue...를 출력한다. 

```
[main] thread starts here...
#### <MyThread-1,LoopTask1> starting...####
#### <MyThread-2,LoopTask2> starting...####
<MyThread-1,LoopTask1> TICK TICK0
#### <MyThread-3,LoopTask3> starting...####
<MyThread-2,LoopTask2> TICK TICK0
<MyThread-3,LoopTask3> TICK TICK0
#### <MyThread-4,LoopTask4> starting...####
<MyThread-4,LoopTask4> TICK TICK0
<MyThread-1,LoopTask1> TICK TICK1
<MyThread-1,LoopTask1> TICK TICK2
<MyThread-2,LoopTask2> TICK TICK1
<MyThread-3,LoopTask3> TICK TICK1
<MyThread-4,LoopTask4> TICK TICK1
<MyThread-3,LoopTask3> TICK TICK2
<MyThread-3,LoopTask3> TICK TICK3
<MyThread-3,LoopTask3> TICK TICK4
<MyThread-2,LoopTask2> TICK TICK2
<MyThread-1,LoopTask1> TICK TICK3
<MyThread-1,LoopTask1> TICK TICK4
#### <MyThread-1,LoopTask1> done...####
<MyThread-2,LoopTask2> TICK TICK3
<MyThread-2,LoopTask2> TICK TICK4
#### <MyThread-2,LoopTask2> done...####
#### <MyThread-3,LoopTask3> done...####
#### <MyThread-3,LoopTask3> LATCH COUNT =1
<MyThread-4,LoopTask4> TICK TICK2
#### <MyThread-2,LoopTask2> LATCH COUNT =2
#### <MyThread-1,LoopTask1> LATCH COUNT =3
<MyThread-4,LoopTask4> TICK TICK3
<MyThread-4,LoopTask4> TICK TICK4
#### <MyThread-4,LoopTask4> done...####
#### <MyThread-4,LoopTask4> LATCH COUNT =0
[main] got the signal to continue...
[main] thread ends here...
```
