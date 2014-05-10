---
layout: post
title: "《Java 7 Concurrency Cookbook 读书笔记》线程管理"
date: 2014-05-10 23:16:49 +0800
comments: true
categories: 读书笔记 java并发
---
**java中创建线程有2种方式：**

      1.继承Thread类，覆盖run方法。
      2.构建实现Runnable接口类，然后创建一个Thread类对象传入一个Runnable对象作为构造参数。

**获取和设置线程信息：**

     ID: 每个线程的独特标识。
     Name: 线程的名称。
     Priority: 线程对象的优先级。优先级别在1-10之间，1是最低级，10是最高级。
     Status: 线程的状态。
     Tips:
     setPriority() 方法会抛出 IllegalArgumentException 异常，如果设置的优先级不是在1-10之间。
     在Java中，线程只能有这6种中的一种状态：  new, runnable, blocked, waiting,
     time waiting, 或 terminated。
**线程的中断：**

       Thread类有一个boolean类型的属性表明线程是否被中断，当调用线程的interrupt()方法,
       此属性被设置为true,isInterrupted() 方法仅返回属性值。
   
       isInterrupted()和interrupted()区别:
       isInterrupted()不会改变interrupted属性值，interrupted()会设置为false，
       interrupted() 方法是一个静态方法，建议使用isInterrupted()方法。
**操作线程的中断机制**

       在Thread类的run方法中捕获InterruptedException异常来控制线程的中断。

        private void do() throws InterruptedException
        { 
         //....
         if (Thread.interrupted()) {
        throw new InterruptedException();
         }
         }
          @Override
          public void run() {
           try {
          do();
          } catch (InterruptedException e) {

        } 
        }
**线程的睡眠与恢复**

      Thread类的 sleep(long millis) 方法 。表示线程休眠millis毫秒。
      TimeUnit枚举元素的sleep(long millis)方法同样能使当前线程睡眠millis毫秒。
      2种线程休眠的方法都会抛出InterruptedException异常。

**等待线程的终结**

      当前线程调用某个线程的join()方法，当前线程会暂停直到被调用线程执行完成。
      join (long milliseconds)
      join (long milliseconds, long nanos)
      
      ex：thread1调用thread2.join(1000)，那么thread1暂停运行，
      直到一下其中一个条件发生，join方法返回：
      thread2结束运行
      1000毫秒过去了
**守护线程的创建和运行**

      在Java中有两类线程：用户线程 (User Thread)、守护线程 (Daemon Thread)。

      守护线程，是指在程序运行的时候在后台提供一种通用服务的线程，
      比如垃圾回收线程就是一个很称职的守护者，并且这种线程并不属于程序中不可或缺的部分。
      因此，当所有的非守护线程结束时，程序也就终止了，同时会杀死进程中的所有守护线程。
      反过来说，只要任何非守护线程还在运行，程序就不会终止。
      
      Thread类的 setDaemon() 方法设置线程为守护线程，此方法必须在调用start()方法之前调用。
      isDaemon() 方法来检查线程是否是守护线程（方法返回 true) 或者是用户线程 (方法返回 false)。

**处理线程非检测异常**

      Java中有2种异常：
      检测异常(Checked Exceptions)：这种异常必须强制捕获或者继续抛出异常（throws）。
      非检测异常(Unchecked Exceptions)：这种异常无需强制捕获。
      在线程对象run()方法中抛出一个检测异常必须捕获，因为run()方法不接受throws抛出异常。
     
      在Java处理Thread中的非检测异常类，需要实现UncaughtExceptionHandler接口。
      public class ExceptionHandler implements UncaughtExceptionHandler {

	  @Override
	  public void uncaughtException(Thread t, Throwable e) {
            
     }
     }
     在线程启动之前设置setUncaughtExceptionHandler(new ExceptionHandler());

    当在一个线程里抛出一个异常，但是这个异常没有被捕获（非检查异常）， 
    JVM 检查线程的相关方法是否有设置一个未捕捉异常的处理者 。
    如果有JVM使用Thread对象和Exception作为参数调用此方法 。
    如果线程没有捕捉未捕获异常的处理者， 那么JVM会把异常的 stack trace 写入操控台并结束任务。
    静态方法 setDefaultUncaughtExceptionHandler() 为应用里的所有线程对象建立异常handler 。
    JVM检测异常3种潜在的Handler：Thread UncaughtExceptionHandler->
    Thread Group UncaughtExceptionHandler->Default Thread 
    UncaughtExceptionHandler->print stack trace
              
**本地线程变量**
   
    ThreadLocal类为线程的变量存储独立的属性值。
    ThreadLocal<Object> threadLocal = new ThreadLocal<Object>() {
        //初始值
		protected Object initialValue() {
			return new Object();
		};
        
		public void set(Object value) {
		};

		public Object get() {
			return new Object();
		};
        //移除此线程局部变量的值。
		public void remove() {
		};

	}; 
    InheritableThreadLocal 类提供线程创建线程的值的遗传性 。
    如果线程A有一个本地线程变量，然后它创建了另一个线程B，
    那么线程B将有与A相同的本地线程变量值。
    你可以覆盖 childValue() 方法来初始子线程的本地线程变量的值。
    它接收父线程的本地线程变量作为参数。
    InheritableThreadLocal<Object> inheritableThreadLocal = 
    new InheritableThreadLocal<Object>() {
		protected Object childValue(Object parentValue) {
			return parentValue;
		};
	};
**线程组**

    Java提供ThreadGroup类来组织线程。 
    ThreadGroup对象可以由Thread对象组成和由另外的ThreadGroup对象组成,生成线程树结构。
    ThreadGroup threadGroup = new ThreadGroup(group name);
    Thread thread=new Thread(threadGroup, runnableimpl);
    activeCount() 和 enumerate() 方法来获取线程个数和与ThreadGroup对象关联的线程的列表。
**处理线程组内非检测异常**

    public class MyThreadGroup extends ThreadGroup {
    public MyThreadGroup(String name) {
    super(name);
    }
    @Override
    public void uncaughtException(Thread t, Throwable e) {
    //覆盖此方法
    }
    }
**线程工厂**

    ThreadFactory线程工厂。
    ThreadFactory 接口只有一个方法是newThread。它接收 Runnable 对象作为参数并返回一个 Thread对象。
    public class MyThreadFactory implements ThreadFactory {
	@Override
	public Thread newThread(Runnable r) {
		return new Thread(r);
	}
    }