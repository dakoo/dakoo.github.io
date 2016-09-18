---
layout: post
title: Java - (멀티쓰레딩 4) 쓰레드에서 값 반환
description: Java - (멀티쓰레딩 4) 쓰레드에서 값 반환
modified: 2016-09-17
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

## 쓰레드에서 값 반환 하기

쓰레드로부터 값을 리턴 받는 방법을 살펴보자. 

일반 쓰레드의 경우를 살펴본 후 Executor의 경우도 살펴보자. 

### 1. 일반 쓰레드의 경우

일반 쓰레드에서 리턴 값을 얻는 방법은 아래 두 가지가 있다. 

- 블록킹: synchronized를 이용해 변경 시점까지 락을 거는 방법
- 논블록킹: Observer Pattern을 이용하는 방법

#### 1.1 블록킹 

synchronized를 이용해 변경 시점까지 락을 거는 방법으로 데이터를 읽는 메소드를 락을 걸고 쓰레드가 실행되는 마지막에 락을 푼다. 그 예는 다음과 같다. this.wait()로 기다리고 this.notifyAll()로 해제한다. 
done 변수를 이용해 synchronized로 무조건 들어가지 않도록 했으며, done은 volatile로 선언해서 어떤 스레드가 값을 변경하든, 항상 최신값을 읽어갈 수 있게 해준다. 
참고로 volatile은 항상 최신 값을 읽게는 해주지만 operation을 atomic하게는 만들지 않으며, synchronized는 operation을 atomic하게 해준다. 즉, volatile은 동기화를 할 뿐이지 lock은 아니다.

```java
import java.util.concurrent.TimeUnit;

public class ReturningValueFirstWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");

		MyThreadTask task1 = new MyThreadTask();
		MyThreadTask task2 = new MyThreadTask();

		new Thread(task1,"firstThread").start();
		new Thread(task2,"secondThread").start();
		
		System.out.println("task1 result:" + task1.getRandomSum());
		System.out.println("task2 result:" + task2.getRandomSum());
		
		System.out.println("Main thread ends here...");
	}
}


class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	private volatile boolean done = false;
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
		/********** Unlock ************/
		done = true;
		synchronized (this) {
			System.out.println(Thread.currentThread().getName() + ": Notifying the result");	
			this.notifyAll();
		}
	}
	
	public int getRandomSum(){
		/*********** Lock ************/
		if(!done) {
			synchronized (this) { 
				try {
					System.out.println(Thread.currentThread().getName() + ": Waiting for the result");	
					this.wait(); 
				} catch (InterruptedException e){
					e.printStackTrace();
				}
			}
			System.out.println(Thread.currentThread().getName() + ": Woken up");	
		}
		return randomSum;
	}

	public MyThreadTask() {
		this.id = ++count;
	}
}
```

결과는 다음과 같다. main 스레드가 getRandomSum()에서 wait()를 하고 있다가 스레드에서 notifyAll()을 한 시점에 풀려나서 값을 읽는 것이다. 
타이밍 상 main 스레드는 한번만 락이 걸렸다가 풀렸음에도 두 스레드 모두 done이 true가 되어 있었다. 

```bash
Main thread starts here...
main: Waiting for the result
<1>TICK TICK 0
<2>TICK TICK 0
<1>TICK TICK 1
<2>TICK TICK 1
<1>TICK TICK 2
<2>TICK TICK 2
<1>TICK TICK 3
<2>TICK TICK 3
<1>TICK TICK 4
<2>TICK TICK 4
secondThread: Notifying the result
firstThread: Notifying the result
main: Woken up
task1 result:1868
task2 result:4049
Main thread ends here...
```

#### 1.2 논블록킹

Observer pattern을 이용하는 것으로 스레드가 끝났을 때 등록된 Listener의 메소드를 호출하는 방식이다. 예제는 다음과 같다. 

```java
import java.util.concurrent.TimeUnit;

public class ReturningValueSecondWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");
		
		MyThreadTask task1 = new MyThreadTask(new RandomSumObserver("task1"));	//Register the listeners
		MyThreadTask task2 = new MyThreadTask(new RandomSumObserver("task2"));	//Register the listeners

		new Thread(task1,"firstThread").start();
		new Thread(task2,"secondThread").start();
		
		System.out.println("Main thread ends here...");
	}
}

/** Listener interface **/
interface ResultListener<T> {
	public void notifyResult(T t);
}

/** Listener **/
class RandomSumObserver implements ResultListener<Integer> {
	
	private String taskId;

	public RandomSumObserver(String taskId) {
		this.taskId = taskId;
	}

	@Override
	public void notifyResult(Integer result) {
		System.out.println(taskId + " result:" + result);
	}
}


class MyThreadTask implements Runnable {	
	private static int count = 0;
	private int id;
	private int randomSum = 0;
	private ResultListener<Integer> listener; //listener

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
		this.listener.notifyResult(randomSum); //Invoke the listener
	}

	public MyThreadTask(ResultListener<Integer> listener) {	//Register listener
		this.id = ++count;
		this.listener = listener;
	}
}
```

결과는 다음과 같다. 블록킹 없이 쓰레드의 연산이 수행되면 자동으로 등록된 Listener를 실행시킨다. 

```bash
Main thread starts here...
Main thread ends here...
<1>TICK TICK 0
<2>TICK TICK 0
<1>TICK TICK 1
<2>TICK TICK 1
<1>TICK TICK 2
<2>TICK TICK 2
<1>TICK TICK 3
<2>TICK TICK 3
<1>TICK TICK 4
<2>TICK TICK 4
task1 result:2004
task2 result:3088
```

### 2. Executor의 경우 

#### 2.1 Callable 인터페이스의 사용

Executor는 Runnable 인터페이스 대신 **Callable 인터페이스**를 이용해 태스크를 만들어서 결과를 반환한다. callable 인터페이스에서 오버라이드해야 하는 메소드는 call()이다. 

```java
public T call() throws Exception
```

ExecutorService.submit()을 하고 Future<T>를 반환 받는다. 

```java
Future<T> ExecutorService.submit(Callable c)
```

ExecutorService.shutdown() 이후에 태스크의 결과는 Future의 get() 메소드를 이용해 반환 받는다. 이때 반드시 try/catch 블록으로 감싸야 한다. 또한 값을 반환하지 않는 Runnable 객체도 동일하게 할 수 있는데 이때 반환 값은 null이다.
또한 Runnable 객체를 submit할때 두번째 아규먼트로 값을 넣는 것이 있는데 이것은 향후 Future로 반환 시 동일한 값이 반환되게 된다.  

자세한 내용은 아래 코드를 참조하자. 

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

public class ReturningValuesUsingExecutorsFirstWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");
		
		ExecutorService execService = Executors.newCachedThreadPool(); 

		//Callable
		Future<Integer> result1 = execService.submit(new MyCallableTask());
		Future<Integer> result2 = execService.submit(new MyCallableTask());
		
		//Runnable
		Future<?> result3 = execService.submit(new MyRunnableTask());
		Future<?> result4 = execService.submit(new MyRunnableTask(), 110.1);		

		execService.shutdown();
	
		try {
			System.out.println("result1:" + result1.get());
			System.out.println("result2:" + result2.get());
			System.out.println("result3:" + result3.get());
			System.out.println("result4:" + result4.get());
		} catch(Exception e) {
			e.printStackTrace();
		}
		
		System.out.println("Main thread ends here...");
	}
}

class MyCallableTask implements Callable<Integer> {
	private static int count = 0;
	private int id;
	private static String taskName = "CallableTaks";
	private int randomSum = 0;

	@Override
	public Integer call() throws Exception {
		for(int i = 0; i<5; i++) {
			System.out.println("<" + taskName +"-"+id + ">TICK TICK " + i);
			randomSum += Math.random()*1000;
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		return randomSum; //값을 반환 
	}
	public MyCallableTask() {
		this.id = ++count;
	}
}

class MyRunnableTask implements Runnable {
	private static int count = 0;
	private int id;
	private static String taskName = "RunnableTaks";

	@Override
	public void run() { 
		for(int i = 0; i<5; i++) {
			System.out.println("<" + taskName +"-"+id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
	}
	public MyRunnableTask() {
		this.id = ++count;
	}
}
```

결과는 다음과 같다. Runnable과 Callable이 동일하게 실행되며, 결과도 반환받는데 Runnable은 null이고, 아규먼트를 추가한 경우 그 아규먼트가 동일하게 반환된다.

```bash
Main thread starts here...
<CallableTaks-1>TICK TICK 0
<CallableTaks-2>TICK TICK 0
<RunnableTaks-1>TICK TICK 0
<RunnableTaks-2>TICK TICK 0
<CallableTaks-1>TICK TICK 1
<RunnableTaks-2>TICK TICK 1
<RunnableTaks-1>TICK TICK 1
<CallableTaks-2>TICK TICK 1
<CallableTaks-1>TICK TICK 2
<RunnableTaks-2>TICK TICK 2
<CallableTaks-2>TICK TICK 2
<RunnableTaks-1>TICK TICK 2
<CallableTaks-1>TICK TICK 3
<RunnableTaks-2>TICK TICK 3
<RunnableTaks-1>TICK TICK 3
<CallableTaks-2>TICK TICK 3
<CallableTaks-1>TICK TICK 4
<RunnableTaks-2>TICK TICK 4
<CallableTaks-2>TICK TICK 4
<RunnableTaks-1>TICK TICK 4
result1:1852
result2:2129
result3:null
result4:110.1
Main thread ends here...
```

#### 2.2 CompletionService의 사용 (1)

CompletionService 인터페이스를 이용해 태스크의 리턴을 획득할 수 있다. 
4개의 인터페이스를 제공하는데 다음과 같다. 

- Future<V> submit(Callable<V> task): Callable 등록
- Future<V> submit(Runnable task, V result); Runnable 등록
- Future<V> take(): 종료된 태스크의 인스턴스. 종료가 될때까지 블록되고 종료가 된 태스크가 있으면 그 태스크의 인스턴스를 반환
- Future<V> poll(): take() 메소드와 동일하지만 블록되지않음. 만약 종료된 태스크가 없으면 null을 반환

주의할 점은  Future<V> submit(Runnable task)가 없다는 것이다. 즉, 결과값을 반환하지 않는 태스크는 처리하지 않는다는 것이다. 
아래 예제는 ReturingValuesUsingExecutorsSecondWay클래스를 제외하고 2.1과 완전히 동일하다. 주의할 것은 앞의 에제에서는 Runnable 객체를 submit할 때 어떤 종류의 것이라도 두번째 아규먼트로 넣을 수 있었으나 CompletionService의 경우는 지정한 타입을 준수해야 한다는 것이다. 

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

public class ReturingValuesUsingExecutorsSecondWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");
		
		ExecutorService execService = Executors.newCachedThreadPool(); 

		CompletionService<Integer> tasks = new ExecutorCompletionService<>(execService);
		
		//Callable
		tasks.submit(new MyCallableTask());
		tasks.submit(new MyCallableTask());
		
		//Runnable
		tasks.submit(new MyRunnableTask(), 110);		

		execService.shutdown();
	
		for(int i = 0; i< 3; i++) {
			try {
				System.out.println("Result: " + tasks.take().get()); //block
			} catch (ExecutionException e) {
				e.printStackTrace();
			}
		}
		
		System.out.println("Main thread ends here...");
	}
}

class MyCallableTask implements Callable<Integer> {
	private static int count = 0;
	private int id;
	private static String taskName = "CallableTaks";
	private int randomSum = 0;

	@Override
	public Integer call() throws Exception {
		for(int i = 0; i<5; i++) {
			System.out.println("<" + taskName +"-"+id + ">TICK TICK " + i);
			randomSum += Math.random()*1000;
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		return randomSum; //값을 반환 
	}
	public MyCallableTask() {
		this.id = ++count;
	}
}

class MyRunnableTask implements Runnable {
	private static int count = 0;
	private int id;
	private static String taskName = "RunnableTaks";

	@Override
	public void run() { 
		for(int i = 0; i<5; i++) {
			System.out.println("<" + taskName +"-"+id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
	}
	public MyRunnableTask() {
		this.id = ++count;
	}
}
```

결과는 다음과 같다. 

```bash
Main thread starts here...
<CallableTaks-1>TICK TICK 0
<CallableTaks-2>TICK TICK 0
<RunnableTaks-1>TICK TICK 0
<CallableTaks-1>TICK TICK 1
<CallableTaks-2>TICK TICK 1
<RunnableTaks-1>TICK TICK 1
<CallableTaks-2>TICK TICK 2
<RunnableTaks-1>TICK TICK 2
<CallableTaks-1>TICK TICK 2
<CallableTaks-2>TICK TICK 3
<CallableTaks-1>TICK TICK 3
<RunnableTaks-1>TICK TICK 3
<RunnableTaks-1>TICK TICK 4
<CallableTaks-1>TICK TICK 4
<CallableTaks-2>TICK TICK 4
Result: 1766
Result: 110
Result: 2772
Main thread ends here...
```


#### 2.3 CompletionService의 사용 (2)

실무에서는 단순한 Integer를 반환하는 것이 아니라 개발자가 정의한 클래스의 객체를 생성해서 반환하는 경우가 많다. 
예를 들어 Generic을 이용해 다음과 같은 반환 값을 위한 클래스를 정의한 경우 어떻게 수정해야 하는지 살펴보자. 

```java
//TaskResult.java
public class TaskResult<S, R> {
	private S taskId;
	private R result;
	public TaskResult(S taskId, R result) {
		this.taskId = taskId;
		this.result = result;
	}
	public S getTaskId() {
		return taskId;
	}
	public R getResult() {
		return result;
	}
	@Override
	public String toString() {
		return "TaskResult [taskId=" + taskId + ", result=" + result + "]";
	}
}
```

아래 코드에서 //type change라고 표시해 놓은 부분이 2.2와 달라진 부분이다.  

```java
public class ReturingValuesUsingExecutorsThirdWay {
	public static void main(String argc[]) throws InterruptedException {
		System.out.println("Main thread starts here...");
		
		ExecutorService execService = Executors.newCachedThreadPool(); 

		CompletionService<TaskResult<String,Integer>> tasks = new ExecutorCompletionService<>(execService); //type change
	
		tasks.submit(new MyCallableTask());
		tasks.submit(new MyCallableTask());
		tasks.submit(new MyRunnableTask(), new TaskResult<String, Integer>("RunnableTask", 101));//type change

		execService.shutdown();
	
		for(int i = 0; i< 3; i++) {
			try {
				System.out.println(tasks.take().get()); 
			} catch (ExecutionException e) {
				e.printStackTrace();
			}
		}
		
		System.out.println("Main thread ends here...");
	}
}

class MyCallableTask implements Callable<TaskResult<String, Integer>> {//type change
	private static int count = 0;
	private int id;
	private static String taskName = "CallableTaks";
	private int randomSum = 0;

	@Override
	public TaskResult<String, Integer> call() throws Exception {//type change
		for(int i = 0; i<5; i++) {
			System.out.println("<" + taskName +"-"+id + ">TICK TICK " + i);
			randomSum += Math.random()*1000;
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
		return new TaskResult<String, Integer>(taskName + id, randomSum); // type change
	}
	public MyCallableTask() {
		this.id = ++count;
	}
}

class MyRunnableTask implements Runnable {
	private static int count = 0;
	private int id;
	private static String taskName = "RunnableTaks";

	@Override
	public void run() { 
		for(int i = 0; i<5; i++) {
			System.out.println("<" + taskName +"-"+id + ">TICK TICK " + i);
			try {
				TimeUnit.MICROSECONDS.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}		
	}
	public MyRunnableTask() {
		this.id = ++count;
	}
}
```

실행 결과는 다음과 같다. 

```bash
Main thread starts here...
<CallableTaks-1>TICK TICK 0
<CallableTaks-2>TICK TICK 0
<RunnableTaks-1>TICK TICK 0
<CallableTaks-1>TICK TICK 1
<CallableTaks-2>TICK TICK 1
<RunnableTaks-1>TICK TICK 1
<CallableTaks-1>TICK TICK 2
<CallableTaks-2>TICK TICK 2
<RunnableTaks-1>TICK TICK 2
<CallableTaks-1>TICK TICK 3
<CallableTaks-2>TICK TICK 3
<RunnableTaks-1>TICK TICK 3
<CallableTaks-1>TICK TICK 4
<CallableTaks-2>TICK TICK 4
<RunnableTaks-1>TICK TICK 4
TaskResult [taskId=CallableTaks2, result=3670]
TaskResult [taskId=CallableTaks1, result=1640]
TaskResult [taskId=RunnableTask, result=101]
Main thread ends here...
```




