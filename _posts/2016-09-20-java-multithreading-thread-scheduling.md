---
layout: post
title: Java - (멀티쓰레딩 10) 쓰레드 스케쥴링
modified: 2016-09-19
tags: [java]
---

## 1. 일반 쓰레드의 스케쥴링 

### 1.1 개요 

쓰레드 스케줄링을 위해 Java Thread는 두가지 API를 제공한다. 

- java.util.Timer
- java.util.TimerTask

#### 1.1.1 java.util.Timer

하나의 쓰레드를 생성하며, 그 하나의 쓰레드에서 모든 태스크를 실행한다. 스케쥴링 로직을 가지고 있어서 다음 태스크를 언제 실행할지 결정하는 API이다. 

```java
- void schedule(TimerTask task, Date time) : 정해진 time에 한번 실행
- void schedule(TimerTask task, long delay) : 현재부터 delay millisec 이후 한번 실행
- void schedule(TimerTask task, Date firstTime, long period): firstTime에 처음 실행하고 period 간격으로 계속 실행
- void schedule(TimerTask task, long delay, long period): 현재부터 delay millisec 이후 처음 실행하고 period 간격으로 계속 실행
- void scheduleAtFixedRate(TimerTask task, Date firstTime, long period): firstTime에 처음 실행하고 period 간격으로 계속 고정 Rate으로 실행
- void scheduleAtFixedRate(TimerTask task, long delay, long period): 현재부터 delay millisec 이후 처음 실행하고 period 간격으로 계속 계속 고정 Rate으로 실행
- void cancel(): Timer를 terminate. **실행되고 있던 태스크는 계속 실행** 되며, 더이상 스케줄은 되지 않음


.TimerTask task: 실행될 태스크
.Date time: 실행 사작할 절대 시간
.long delay: 현재 시간으로부터의 delay offset. Milliseconds
.long period: 태스크 실행시간 간의 고정 delay


#### 1.1.2 java.util.TimerTask

스케줄링 대상이 되는 태스크 API이다. 

Runnable 인터페이스를 구현한 것이다. 반복된 실행을 위해 짧게 수행(short-lived)되어져야 한다. 

```java
long scheduleExecutionTime(): 동작하는 시간으로 가장 최신에 실행된 시간을 반환한다. 
boolean cancel(): 태스크를 취소한다. 만약 태스크가 구동중이면 구동은 지속되지만 다시 실행되지 않는다. 
```

#### 1.1.3 정리

- Timer 객체당 한번에 하나의 태스크만 실행된다. 
- Timer 태스크는 재빨리 종료되어야 한다. 
- Application이 종료될 때 반드시 Timer.cancel()이 호출되어야 한다. 그렇지 않으면 leakage가 발생한다. 
- Timer가 cancel되면 더이상 태스크는 스케줄되지 않는다. 
- Timer 클래스는 thread-safe이다. 
- Timer 클래스는 실시간성을 보장하지 않는다. 


### 1.2 한번 실행되는 태스크 스케줄링하기 

다음 두 메소드를 이용해서 태스크를 한번 스케줄링 할 수 있다. 

- void schedule(TimerTask task, Date time) : 정해진 time에 한번 실행
- void schedule(TimerTask task, long delay) : 현재부터 delay millisec 이후 한번 실행

예제는 다음과 같다. 

```java
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;
import java.util.Timer;
import java.util.TimerTask;
import java.util.concurrent.TimeUnit;

public class SchedulingTasksForOneTimeExecution {
	private static SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("Main thread starts here...");
	
		Timer timer = new Timer("Timer-thread", false); //false: user thread, true: daemon
		Date currentTime = new Date();
		System.out.println("["+currentThreadName+"] Current time" + dateFormatter.format(currentTime));
		

		Date scheduledTime = TimeUtils.getFutureTime(currentTime, 5000);
		ScheduledTask task0 = new ScheduledTask(100);
		timer.schedule(task0, scheduledTime); //schedule(TimerTask task, Date time)
		System.out.println("["+currentThreadName+"] Task-0 is scheduled for running at " + dateFormatter.format(currentTime));
		
		long delayMillis = 10000;
		ScheduledTask task1 = new ScheduledTask(100);
		timer.schedule(task1, delayMillis); //schedule(TimerTask task, long delay)
		System.out.println("["+currentThreadName+"] Task-1 is scheduled for running "+ delayMillis/1000 + " at " + dateFormatter.format(currentTime));

		ScheduledTask task2 = new ScheduledTask(100);
		timer.schedule(task2, delayMillis); //schedule(TimerTask task, long delay)
		System.out.println("["+currentThreadName+"] Task-2 is scheduled for running "+ delayMillis/1000 + " at " + dateFormatter.format(currentTime));

		task1.cancel();	//task1 canceled
		
		TimeUnit.MILLISECONDS.sleep(12000);
		timer.cancel();
		System.out.println("Main thread ends here...");
	}
}


class ScheduledTask extends TimerTask {	
	private static int count = 0;
	private long sleepTime;
	private String taskId;
	private SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	@Override
	public void run(){
		Date startTime = new Date();
		Date SchdulingForRunningTime = new Date(super.scheduledExecutionTime()); 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId 
				+ " > Scheduled to run at "
				+ dateFormatter.format(SchdulingForRunningTime) 
				+ ", Actually started at "
				+ dateFormatter.format(startTime) 
				+ "####");

		for(int i = 0; i<5; i++) {
			try {
				TimeUnit.MICROSECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}

		System.out.println("#### <" + currentThreadName + "," + taskId  
				+ " >Finished at "
				+ dateFormatter.format(new Date()) 
				+ "####");
	}
	

	public ScheduledTask(long sleepTime) {
		this.sleepTime = sleepTime;
		this.taskId = "ScheduledTask-" + count++;
	}
}

/* Utility class to get the future time*/
class TimeUtils {
	private TimeUtils() {
		
	}
	public static Date getFutureTime(Date initialTime, long millisToAdd) {
		Calendar cal = GregorianCalendar.getInstance();
		cal.setTimeInMillis(initialTime.getTime() + millisToAdd);
		return cal.getTime();
	}
}
```

결과는 다음과 같다. task0, 1, 2 모두 스케줄링을 한 후 task1을 취소했더니 task0와 task2만 실행되고 task1는 실행되지 않음을 알 수 있다. 

```bash
Main thread starts here...
[main] Current time20-09-2016 23:49:32.518
[main] Task-0 is scheduled for running at 20-09-2016 23:49:32.518
[main] Task-1 is scheduled for running 10 at 20-09-2016 23:49:32.518
[main] Task-2 is scheduled for running 10 at 20-09-2016 23:49:32.518
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 20-09-2016 23:49:37.518, Actually started at 20-09-2016 23:49:37.521####
#### <Timer-thread,ScheduledTask-0 >Finished at 20-09-2016 23:49:37.518####
#### <Timer-thread,ScheduledTask-2 > Scheduled to run at 20-09-2016 23:49:42.519, Actually started at 20-09-2016 23:49:42.522####
#### <Timer-thread,ScheduledTask-2 >Finished at 20-09-2016 23:49:42.519####
Main thread ends here...
```

### 1.3 반복되는 태스크 스케줄링하기 

아래 메소드를 이용하여 반복되는 태스크를 스케줄링한다. 

- void schedule(TimerTask task, Date firstTime, long period): firstTime에 처음 실행하고 period 간격으로 계속 실행
- void schedule(TimerTask task, long delay, long period): 현재부터 delay millisec 이후 처음 실행하고 period 간격으로 계속 실행

간격은 이전 태스크 시작부터 다음 태스크 시작까지이다. 만약 정한 간격보다 길어지면 시작 시간이 밀려서 시작하게 된다. 

```java
package a1;

import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;
import java.util.Timer;
import java.util.TimerTask;
import java.util.concurrent.TimeUnit;

public class SchedulingTasksFixedDelayRepeatedExecution {
	private static SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("Main thread starts here...");
	
		Timer timer = new Timer("Timer-thread", false); //false: user thread, true: daemon
		Date currentTime = new Date();
		System.out.println("["+currentThreadName+"] Current time" + dateFormatter.format(currentTime));
		

		Date scheduledTime = TimeUtils.getFutureTime(currentTime, 1000);
		ScheduledTask task0 = new ScheduledTask(100);
		long periodMillis = 1000;
		timer.schedule(task0, scheduledTime, periodMillis); //schedule(TimerTask task, Date firstTime, long period)
		System.out.println("["+currentThreadName+"] Task-0 is scheduled for running at " + dateFormatter.format(currentTime));
		
		long delayMillis = 5000;
		periodMillis = 5000;
		ScheduledTask task1 = new ScheduledTask(100);
		timer.schedule(task1, delayMillis, periodMillis); //schedule(TimerTask task, long delay, long period)
		System.out.println("["+currentThreadName+"] Task-1 is scheduled for running "+ delayMillis/1000 + " at " + dateFormatter.format(currentTime));

		TimeUnit.MILLISECONDS.sleep(11000);
		timer.cancel();
		System.out.println("Main thread ends here...");
	}
}


class ScheduledTask extends TimerTask {	
	private static int count = 0;
	private long sleepTime;
	private String taskId;
	private SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	@Override
	public void run(){
		Date startTime = new Date();
		Date SchdulingForRunningTime = new Date(super.scheduledExecutionTime()); 
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId 
				+ " > Scheduled to run at "
				+ dateFormatter.format(SchdulingForRunningTime) 
				+ ", Actually started at "
				+ dateFormatter.format(startTime) 
				+ "####");

		for(int i = 0; i<5; i++) {
			try {
				TimeUnit.MICROSECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}

		System.out.println("#### <" + currentThreadName + "," + taskId  
				+ " >Finished at "
				+ dateFormatter.format(new Date()) 
				+ "####");
	}
	

	public ScheduledTask(long sleepTime) {
		this.sleepTime = sleepTime;
		this.taskId = "ScheduledTask-" + count++;
	}
}

/* Utility class to get the future time*/
class TimeUtils {
	private TimeUtils() {
		
	}
	public static Date getFutureTime(Date initialTime, long millisToAdd) {
		Calendar cal = GregorianCalendar.getInstance();
		cal.setTimeInMillis(initialTime.getTime() + millisToAdd);
		return cal.getTime();
	}
}
```

결과는 다음과 같다. task0은 1초간격으로 계속 실행되고, task1은 5초간격으로 실행되다가 timer.cancel()이 호출되면 종료된다. 

```bash
Main thread starts here...
[main] Current time21-09-2016 00:02:31.106
[main] Task-0 is scheduled for running at 21-09-2016 00:02:31.106
[main] Task-1 is scheduled for running 5 at 21-09-2016 00:02:31.106
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:32.109, Actually started at 21-09-2016 00:02:32.109####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:32.109####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:33.111, Actually started at 21-09-2016 00:02:33.111####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:33.111####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:34.115, Actually started at 21-09-2016 00:02:34.115####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:34.115####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:35.119, Actually started at 21-09-2016 00:02:35.119####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:35.119####
#### <Timer-thread,ScheduledTask-1 > Scheduled to run at 21-09-2016 00:02:36.109, Actually started at 21-09-2016 00:02:36.109####
#### <Timer-thread,ScheduledTask-1 >Finished at 21-09-2016 00:02:36.109####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:36.120, Actually started at 21-09-2016 00:02:36.120####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:36.120####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:37.122, Actually started at 21-09-2016 00:02:37.122####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:37.122####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:38.124, Actually started at 21-09-2016 00:02:38.124####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:38.124####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:39.128, Actually started at 21-09-2016 00:02:39.128####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:39.128####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:40.129, Actually started at 21-09-2016 00:02:40.129####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:40.129####
#### <Timer-thread,ScheduledTask-1 > Scheduled to run at 21-09-2016 00:02:41.113, Actually started at 21-09-2016 00:02:41.113####
#### <Timer-thread,ScheduledTask-1 >Finished at 21-09-2016 00:02:41.113####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:41.129, Actually started at 21-09-2016 00:02:41.129####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:41.129####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:42.131, Actually started at 21-09-2016 00:02:42.131####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:42.131####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:43.136, Actually started at 21-09-2016 00:02:43.136####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:43.136####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:44.139, Actually started at 21-09-2016 00:02:44.139####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:44.139####
#### <Timer-thread,ScheduledTask-0 > Scheduled to run at 21-09-2016 00:02:45.144, Actually started at 21-09-2016 00:02:45.144####
#### <Timer-thread,ScheduledTask-0 >Finished at 21-09-2016 00:02:45.144####
Main thread ends here...
```

### 1.4 FixedRate로 반복되는 태스크 스케줄링하기 

아래 메소드를 이용하여 반복되는 태스크를 스케줄링한다. 

- void scheduleAtFixedRate(TimerTask task, Date firstTime, long period)
- void scheduleAtFixedRate(TimerTask task, long delay, long period)

시작하는 시점은 맨 처음 태스크 실행 시간을 기준으로 고정되어 계산 된다. FixedRate가 아닌 경우는 바로 이전의 시작을 기준으로 계산한다. 
만약 정한 간격보다 길어지면 시작 시간이 밀려서 시작하는 것이 아니라 바로 그 시간에 시작한다. 그러므로 절대 시간에 관한 요구사항을 가진 경우 FixedRate을 사용한다. 

컨셉을 이해한다면 이전 예와 동일하므로 에제를 가지고 실행하지 않는다. 

## 2. Executor 태스크 스케줄링

### 2.1 개요 

태스크 스케줄링을 위해 Executors는 다음 두개의 쓰레드 풀을 별도로 제공한다. 

- Single-thread-scheduled-executor: 하나의 쓰레드로 여러 태스크의 스케줄링
- Scheduled-thread-pool: 여러 쓰레드로 여러 태스크의 스케줄링

Executors 클래스를 사용해 생성하는 것은 다음과 같이 한다. 

```java
static ScheduledExecutorService newSingleThreadScheduledExecutor()
static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)
```

ScheduledExecutorService 인터페이스는 ExecutorService 인터페이스를 확장한 것으로 다음과 같은 메소드를 정의하고 있다. 

```java
ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit)
ScheduledFuture<?> schedule(Callable<V> callable, long delay, TimeUnit unit)
ScheduledFuture<?> scheduleWithFixedDelay(Runnable, long initialDelay, long delay, TimeUnit unit)
ScheduledFuture<?> scheduleWithFixedRate(Runnable, long initialDelay, long delay, TimeUnit unit)
```

일반 쓰레드와 다른 점은 정확한 시간을 지정하여 구동하는 것이 없다는 것이다. 


ScheduledFuture 인터페이스는 Future 인터페이스와 Delayed 인터페이스를 상속한다. Delayed 인터페이스는 다음의 메소드만을 정의한다. 이것은 다음 실행 스케줄까지 얼마나 남았는지 반환한다. 

```java
long getDelay(TimeUnit unit)
```


### 2.2 한번 실행되는 태스크 스케줄링하기 

다음 두 메소드를 이용해서 태스크를 한번 스케줄링 할 수 있다. 

- ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit)
- ScheduledFuture<?> schedule(Callable<V> callable, long delay, TimeUnit unit)

실행은 Single-thread-scheduled-executor 또는 Scheduled-thread-pool 중 하나를 사용하여 스케줄될 수 있다. 
TimerTask는 필요없으며 Runnable 또는 Callable이면 된다. 

예제는 다음과 같다. 

```java
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;
import java.util.Timer;
import java.util.TimerTask;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.ScheduledFuture;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.TimeUnit;

public class SchedulingTasksFixedDelayRepeatedExecution {
	private static SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("Main thread starts here...");
	
		ScheduledExecutorService execService = Executors.newSingleThreadScheduledExecutor(new NamedThreadsFactory());
		
		System.out.println("["+currentThreadName+"] Current time" + dateFormatter.format(new Date()));
		
		ScheduledFuture<?> schedFuture1 = execService.schedule(new ScheduledRunnableTask(0), 2, TimeUnit.SECONDS);
		ScheduledFuture<Integer> schedFuture2 = execService.schedule(new ScheduledCallableTask(0), 4, TimeUnit.SECONDS);
		
		execService.shutdown();
		try {
			System.out.println("Task1 result = " + schedFuture1.get());
			System.out.println("Task2 result = " + schedFuture2.get());
		} catch (ExecutionException e) {
			e.printStackTrace();
		}

		System.out.println("Main thread ends here...");
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

class ScheduledRunnableTask implements Runnable {	
	private static int count = 0;
	private long sleepTime;
	private String taskId;
	private SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	@Override
	public void run(){
		Date startTime = new Date();
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId 
				+ " > Actually started at "
				+ dateFormatter.format(startTime) 
				+ "####");

		for(int i = 0; i<5; i++) {
			try {
				TimeUnit.MICROSECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}

		System.out.println("#### <" + currentThreadName + "," + taskId  
				+ " >Finished at "
				+ dateFormatter.format(new Date()) 
				+ "####");
	}
	

	public ScheduledRunnableTask(long sleepTime) {
		this.sleepTime = sleepTime;
		this.taskId = "ScheduledRunnableTask-" + count++;
	}
}

class ScheduledCallableTask implements Callable<Integer> {	
	private static int count = 0;
	private long sleepTime;
	private String taskId;
	private SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	@Override
	public Integer call() throws Exception {
		Date startTime = new Date();
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId 
				+ " > Actually started at "
				+ dateFormatter.format(startTime) 
				+ "####");

		for(int i = 0; i<5; i++) {
			try {
				TimeUnit.MICROSECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}

		System.out.println("#### <" + currentThreadName + "," + taskId  
				+ " >Finished at "
				+ dateFormatter.format(new Date()) 
				+ "####");
		return 10; //whatever
	}
	

	public ScheduledCallableTask(long sleepTime) {
		this.sleepTime = sleepTime;
		this.taskId = "ScheduledCallableTask-" + count++;
	}
}

```

결과는 다음과 같다. Runnable 태스크가 2초, Callable이 4초후에 실행되었다. 

```bash
Main thread starts here...
[main] Current time21-09-2016 00:42:41.359
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:42:43.365####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:42:43.365####
Task1 result = null
#### <MyThread-1,ScheduledCallableTask-0 > Actually started at 21-09-2016 00:42:45.362####
#### <MyThread-1,ScheduledCallableTask-0 >Finished at 21-09-2016 00:42:45.362####
Task2 result = 10
Main thread ends here...
```

### 2.3 반복 실행되는 태스크 스케줄링하기 

반복 실행되는 태스크는 다음의 메소드를 이용한다. 

- ScheduledFuture<?> scheduleWithFixedDelay(Runnable, long initialDelay, long delay, TimeUnit unit)

주의할 점은 다음과 같다. 

- 인터벌은 이전 태스크 실행이 언제 끝났느냐에 따라 달라진다. 일반 쓰레드가 시작을 기준으로 정하는 것과 구별된다. 
- Callable 태스크의 반복은 지원하지 않는다. Runnable만 지원된다. 

예제는 다음과 같다. 4초후에 태스크가 실행되고 매 2초마다 반복되는 것이다. 

```java
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;
import java.util.Timer;
import java.util.TimerTask;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.ScheduledFuture;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.TimeUnit;

public class SchedulingTasksFixedDelayRepeatedExecution {
	private static SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("Main thread starts here...");
	
		ScheduledExecutorService execService = Executors.newSingleThreadScheduledExecutor(new NamedThreadsFactory());
		
		System.out.println("["+currentThreadName+"] Current time" + dateFormatter.format(new Date()));
		
		//ScheduledFuture<?> scheduleWithFixedDelay(Runnable, long initialDelay, long delay, TimeUnit unit)
		ScheduledFuture<?> schedFuture = execService.scheduleWithFixedDelay(new ScheduledRunnableTask(0), 4, 2, TimeUnit.SECONDS);
		
		TimeUnit.MILLISECONDS.sleep(10000);
		execService.shutdown();

		System.out.println("Main thread ends here...");
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

class ScheduledRunnableTask implements Runnable {	
	private static int count = 0;
	private long sleepTime;
	private String taskId;
	private SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	@Override
	public void run(){
		Date startTime = new Date();
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("#### <" + currentThreadName +"," + taskId 
				+ " > Actually started at "
				+ dateFormatter.format(startTime) 
				+ "####");

		for(int i = 0; i<5; i++) {
			try {
				TimeUnit.MICROSECONDS.sleep(sleepTime);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}

		System.out.println("#### <" + currentThreadName + "," + taskId  
				+ " >Finished at "
				+ dateFormatter.format(new Date()) 
				+ "####");
	}
	

	public ScheduledRunnableTask(long sleepTime) {
		this.sleepTime = sleepTime;
		this.taskId = "ScheduledRunnableTask-" + count++;
	}
}
```

결과는 다음과 같다. 

```bash
Main thread starts here...
[main] Current time21-09-2016 00:50:15.020
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:50:19.027####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:50:19.027####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:50:21.031####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:50:21.031####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:50:23.035####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:50:23.036####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:50:25.039####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:50:25.039####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:50:27.043####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:50:27.043####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:50:29.048####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:50:29.048####
Main thread ends here...
```


### 2.4 FixedRate로 반복 실행되는 태스크 스케줄링하기 

반복 실행되는 태스크는 다음의 메소드를 이용한다. 

- ScheduledFuture<?> scheduleAtFixedRate(Runnable, long initialDelay, long delay, TimeUnit unit)

주의할 점은 다음과 같다. 

- 맨처음 태스크의 **시작 시간**을 기준으로 각각 실행이 스케줄 된다. 일반 쓰레드의 FixedRate와 동일한 점이다.  
- Callable 태스크의 반복은 지원하지 않는다. Runnable만 지원된다. 

예제는 다음과 같다. 4초후에 태스크가 실행되고 매 2초마다 반복되는 것으로 scheduleWithFixedDelay의 경우와 메소드 이름만 다를 뿐이다. 

```java
public class SchedulingTasksFixedDelayRepeatedExecution {
	private static SimpleDateFormat dateFormatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss.SSS");

	public static void main(String argc[]) throws InterruptedException {
		String currentThreadName = Thread.currentThread().getName();
		System.out.println("Main thread starts here...");
	
		ScheduledExecutorService execService = Executors.newSingleThreadScheduledExecutor(new NamedThreadsFactory());
		
		System.out.println("["+currentThreadName+"] Current time" + dateFormatter.format(new Date()));
		
		//ScheduledFuture<?> schedFuture = execService.scheduleWithFixedDelay(new ScheduledRunnableTask(0), 4, 2, TimeUnit.SECONDS);
		ScheduledFuture<?> schedFuture = execService.scheduleAtFixedRate(new ScheduledRunnableTask(0), 4, 2, TimeUnit.SECONDS);
		
		TimeUnit.MILLISECONDS.sleep(10000);
		execService.shutdown();

		System.out.println("Main thread ends here...");
	}
}
```

결과는 다음과 같다. 

```bash
Main thread starts here...
[main] Current time21-09-2016 00:54:42.461
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:54:46.467####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:54:46.468####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:54:48.464####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:54:48.464####
#### <MyThread-1,ScheduledRunnableTask-0 > Actually started at 21-09-2016 00:54:50.466####
#### <MyThread-1,ScheduledRunnableTask-0 >Finished at 21-09-2016 00:54:50.466####
Main thread ends here...
```
