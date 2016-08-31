<!--
author: wngn123
head: head.png
date: 2016-08-30
title: java源码 Thread
tags: java
category: Java
status: publish
summary: 阅读Java的源码，Thread源码
-->

## Thread继承体系 ##
Thread的签名如下，继承了实现Runnable接口
```java
public class Thread implements Runnable
```
## Thread基本概念 ##
线程 是程序中的执行线程。Java 虚拟机允许应用程序并发地运行多个执行线程。 
每个线程都有一个优先级，高优先级线程的执行优先于低优先级线程。每个线程都可以或不可以标记为一个守护程序。当某个线程中运行的代码创建一个新 Thread 对象时，该新线程的初始优先级被设定为创建线程的优先级，并且当且仅当创建线程是守护线程时，新线程才是守护程序。 

当 Java 虚拟机启动时，通常都会有单个非守护线程（它通常会调用某个指定类的 main 方法）。Java 虚拟机会继续执行线程，直到下列任一情况出现时为止： 

调用了 Runtime 类的 exit 方法，并且安全管理器允许退出操作发生。 
非守护线程的所有线程都已停止运行，无论是通过从对 run 方法的调用中返回，还是通过抛出一个传播到 run 方法之外的异常。 

每个线程都有一个标识名，多个线程可以同名。如果线程创建时没有指定标识名，就会为其生成一个新名称
## Thread的属性 ##
```
	//线程名
	private char name[];
    //线程优先级
    private int priority;
    /* Whether or not to single_step this thread. */
    private boolean single_step;
    //是否是守护线程
    private boolean daemon = false;
    //虚拟机状态
    private boolean stillborn = false;
    //实际运行的对象，Runnable实例
    private Runnable target;
    //线程组对象
    private ThreadGroup group;
    //上下文类加载器
    private ClassLoader contextClassLoader;
    /* The inherited AccessControlContext of this thread */
    private AccessControlContext inheritedAccessControlContext;
    ThreadLocal.ThreadLocalMap threadLocals = null;
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    private long stackSize;
    private long nativeParkEventPointer;
    private long tid;
    private static long threadSeqNumber;
    private volatile int threadStatus = 0;
    //interrupt status(中断状态)
    volatile Object parkBlocker;
    private volatile Interruptible blocker;
    private final Object blockerLock = new Object();
    //最小优先级
    public final static int MIN_PRIORITY = 1;
   //默认优先级
    public final static int NORM_PRIORITY = 5;
    //最大优先级
    public final static int MAX_PRIORITY = 10;
```

## Thread构造函数 ##
构造线程需要:ThreadGroup线程组, Runnable线程实例, name线程名, stackSize线程栈大小 AccessControlContext acc

如果没有设置线程组，则使用父线程线程组

如果没有设置线程实例，则为null

如果没有设置线程名称，则默认为：Thread-nextThreadNum

如果没有设置线程栈大小，则为0


```
	public Thread() {
        init(null, null, "Thread-" + nextThreadNum(), 0);
    }

    public Thread(Runnable target) {
        init(null, target, "Thread-" + nextThreadNum(), 0);
    }

    Thread(Runnable target, AccessControlContext acc) {
        init(null, target, "Thread-" + nextThreadNum(), 0, acc);
    }

    public Thread(ThreadGroup group, Runnable target) {
        init(group, target, "Thread-" + nextThreadNum(), 0);
    }

    public Thread(String name) {
        init(null, null, name, 0);
    }

    public Thread(ThreadGroup group, String name) {
        init(group, null, name, 0);
    }

    public Thread(Runnable target, String name) {
        init(null, target, name, 0);
    }

    public Thread(ThreadGroup group, Runnable target, String name) {
        init(group, target, name, 0);
    }

    public Thread(ThreadGroup group, Runnable target, String name,
                  long stackSize) {
        init(group, target, name, stackSize);
    }
    
    private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize) {
        init(g, target, name, stackSize, null);
    }
    
    private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc) {
        //线程名称不能为空
        if (name == null) {
            throw new NullPointerException("name cannot be null");
        }
        this.name = name.toCharArray();
		//设置父线程为当前线程
        Thread parent = currentThread();
        //获取JVM安全管理器
        SecurityManager security = System.getSecurityManager();
        if (g == null) {
            /* Determine if it's an applet or not */
            /* If there is a security manager, ask the security manager
               what to do. */
            if (security != null) {
            	//返回一个新的线程被实例化的ThreadGroup
                g = security.getThreadGroup();
            }
            /* If the security doesn't have a strong opinion of the matter
               use the parent thread group. */
            if (g == null) {
                g = parent.getThreadGroup();
            }
        }
        //检测当前线程是否有操作线程组的权限
        g.checkAccess();
        /*
         * Do we have the required permissions?
         */
        if (security != null) {
            if (isCCLOverridden(getClass())) {
                security.checkPermission(SUBCLASS_IMPLEMENTATION_PERMISSION);
            }
        }
        //线程组添加一个未启动线程，线程组中未启动线程数量加一
        g.addUnstarted();
        this.group = g;
        //继承父线程的守护线程和优先级，类加载器属性
        this.daemon = parent.isDaemon();
        this.priority = parent.getPriority();
        if (security == null || isCCLOverridden(parent.getClass()))
            this.contextClassLoader = parent.getContextClassLoader();
        else
            this.contextClassLoader = parent.contextClassLoader;
        this.inheritedAccessControlContext =
                acc != null ? acc : AccessController.getContext();
        this.target = target;
        setPriority(priority);
        if (parent.inheritableThreadLocals != null)
            this.inheritableThreadLocals =
                ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
        /* Stash the specified stack size in case the VM cares */
        this.stackSize = stackSize;

        /* Set thread ID */
        tid = nextThreadID();
    }
```

```
private static int threadInitNumber;
    private static synchronized int nextThreadNum() {
        return threadInitNumber++;
    }
    private static synchronized long nextThreadID() {
        return ++threadSeqNumber;
    }
```
## Thread的native方法 ##
```
     //返回当前线程执行对象
    public static native Thread currentThread();
    //使当前执行线程暂停一会，让其它线程得以执行。只是临时让出时间片，不会释放拥有的锁。
    public static native void yield();
    //使当前执行线程休眠指定的时间，不释放持有的锁
    public static native void sleep(long millis) throws InterruptedException;
    //测试线程是否被中断，中断状态是否被重置依赖于传入的ClearInterrupted参数值
    private native boolean isInterrupted(boolean ClearInterrupted);
    //测试线程是否活着，如果线程启动且没有死亡，那么线程是活跃状态
    public final native boolean isAlive();
    //计算该线程中的堆栈帧数。线程必须挂起。
    public native int countStackFrames();
    //当且仅当当前线程在指定的对象上保持监视器锁时，才返回true。该方法旨在使程序能够断言当前线程已经保持一个指定的锁
    public static native boolean holdsLock(Object obj);
    //导出线程栈数据
    private native static StackTraceElement[][] dumpThreads(Thread[] threads);
    //获取所有的线程快照
    private native static Thread[] getThreads();
    //设置线程优先级
    private native void setPriority0(int newPriority);
    //线程启动
    private native void start0();
    //停止线程
    private native void stop0(Object o);
    //状态当前线程
    private native void suspend0();
    //继续执行当前线程
    private native void resume0();
    //这种当前线程的中断标识（没有中断）
    private native void interrupt0();
```

## Thread的ID ##

```
    //默认线程名的自增
	private static int threadInitNumber;
    private static synchronized int nextThreadNum() {
        return threadInitNumber++;
    }
    //线程ID的自增
    private static long threadSeqNumber;
	private static synchronized long nextThreadID() {
        return ++threadSeqNumber;
    }
```

## Thread启动 ##

```
	public synchronized void start() {
        if (threadStatus != 0)
            throw new IllegalThreadStateException();
        //通知线程组线程启动，该线程会被添加到线程组活跃线程数组中，并且活跃线程数加一，未启动线程数量减一
        group.add(this);
        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                	//启动失败，线程组中未启动线程数量加一，活跃线程数减一，并且从线程组中移除该线程
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
    private native void start0();
```
## Thread睡眠 ##
``` 
	//睡眠：毫米，纳秒
	public static void sleep(long millis, int nanos) throws InterruptedException {
        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }
        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }
        if (nanos >= 500000 || (nanos != 0 && millis == 0)) {
            millis++;
        }

        sleep(millis);
    }
    public static native void sleep(long millis) throws InterruptedException;

```
## Thread的中断 ##
线程的thread.interrupt()方法是中断线程，将会设置该线程的中断状态位，即设置为true，中断的结果线程是死亡、还是等待新的任务或是继续运行至下一步，就取决于这个程序本身。线程会不时地检测这个中断标示位，以判断线程是否应该被中断（中断标示值是否为true）。它并不像stop方法那样会中断一个正在运行的线程。

判断某个线程是否已被发送过中断请求，请使用Thread.currentThread().isInterrupted()方法（因为它将线程中断标示位设置为true后，不会立刻清除中断标示位，即不会将中断标设置为false），而不要使用thread.interrupted()（该方法调用后会将中断标示位清除，即重新设置为false）方法来判断。

如果一个线程处于了阻塞状态（如线程调用了thread.sleep、thread.join、thread.wait、1.5中的condition.await、以及可中断的通道上的 I/O 操作方法后可进入阻塞状态），则在线程在检查中断标示时如果发现中断标示为true，则会在这些阻塞方法（sleep、join、wait、1.5中的condition.await及可中断的通道上的 I/O 操作方法）调用处抛出InterruptedException异常，并且在抛出异常后立即将线程的中断标示位清除，即重新设置为false。抛出异常是为了线程从阻塞状态醒过来，并在结束线程前让程序员有足够的时间来处理中断请求。

没有任何语言方面的需求一个被中断的线程应该终止。中断一个线程只是为了引起该线程的注意，被中断线程可以决定如何应对中断。某些线程非常重要，以至于它们应该不理会中断，而是在处理完抛出的异常之后继续执行，但是更普遍的情况是，一个线程将把中断看作一个终止请求

```
    //interrupt status(中断状态)
    volatile Object parkBlocker;
	//测试线程中断但不清除中断状态
	public boolean isInterrupted() {
        return isInterrupted(false);
    }
    //测试线程中断并清除中断状态，parkBlocker会被置为空
    public static boolean interrupted() {
        return currentThread().isInterrupted(true);
    }
    public void interrupt() {
        if (this != Thread.currentThread())
            checkAccess();
        synchronized (blockerLock) {
            Interruptible b = blocker;
            if (b != null) {
                interrupt0();           // Just to set the interrupt flag
                b.interrupt(this);
                return;
            }
        }
        interrupt0();
    }
    private native void interrupt0();
    private native boolean isInterrupted(boolean ClearInterrupted);
```
## Thread的join ##
join() 方法是让调用该方法的主线程执行run()时暂时卡住，等run()执行完成后， 主线程再调用执行join()后面的代码。
join()方法会是当前线程等待，
```
	public static void main(String[] args) throws InterruptedException {  
        Thread t1 = new Thread(new ThreadTesterA());  
        Thread t2 = new Thread(new ThreadTesterB());  
        t1.start();  
        t1.join(); // wait t1 to be finished  
        t2.start();  
        t2.join(); // in this program, this may be removed  
    }  
```
t1.join()会是当前线程（即main线程处于等待状态，器等待t1线程结束才会继续执行）

```
	//让当前线程一直等待，知道线程结束
 	public final void join() throws InterruptedException {
        join(0);
    }
	//让当前线程等待一段时间执行，如果参数为0则永远等待
	public final synchronized void join(long millis) throws InterruptedException {
        long base = System.currentTimeMillis();
        long now = 0;
        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }
        //如果millis=0，则一直等待，知道线程结束
        if (millis == 0) {
            while (isAlive()) {
                wait(0);
            }
        } else {
            while (isAlive()) {
                long delay = millis - now;
                if (delay <= 0) {
                    break;
                }
                // 在其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者超过指定的时间量前，导致当前线程等待。
                wait(delay);
                now = System.currentTimeMillis() - base;
            }
        }
    }
    public final synchronized void join(long millis, int nanos） throws InterruptedException {
        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }
        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException("nanosecond timeout value out of range");
        }
        if (nanos >= 500000 || (nanos != 0 && millis == 0)) {
            millis++;
        }
        join(millis);
    }
```
