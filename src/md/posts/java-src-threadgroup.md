<!--
author: wngn123
head: head.png
date: 2016-08-30
title: java源码 ThreadGroup
tags: java
category: Java
status: publish
summary: 阅读Java的源码，ThreadGroup源码
-->

## ThreadGroup继承体系 ##
```
public class ThreadGroup implements Thread.UncaughtExceptionHandler
```
Thread的run方法是不抛出任何检查型异常(checked exception)的,但是它自身却可能因为一个异常而被终止，导致这个线程的终结。最麻烦的是，在线程中抛出的异常即使使用try...catch也无法截获，因此可能导致一些问题出现，比如异常的时候无法回收一些系统资源，或者没有关闭当前的连接等等。 
```
public interface UncaughtExceptionHandler {
    void uncaughtException(Thread t, Throwable e);
}
```
```
    public void uncaughtException(Thread t, Throwable e) {
        if (parent != null) {
            parent.uncaughtException(t, e);
        } else {
            Thread.UncaughtExceptionHandler ueh =
                Thread.getDefaultUncaughtExceptionHandler();
            if (ueh != null) {
                ueh.uncaughtException(t, e);
            } else if (!(e instanceof ThreadDeath)) {
                System.err.print("Exception in thread \""
                                 + t.getName() + "\" ");
                e.printStackTrace(System.err);
            }
        }
    }
```

## ThreadGroup基本属性 ##
线程组表示一个线程的集合。此外，线程组也可以包含其他线程组。线程组构成一棵树，在树中，除了初始线程组外，每个线程组都有一个父线程组。 

允许线程访问有关自己的线程组的信息，但是不允许它访问有关其线程组的父线程组或其他任何线程组的信息。 
```
	//父线程组
	private final ThreadGroup parent;
    线程组名称
    String name;
    //线程最大优先级
    int maxPriority;
    //是否被销毁
    boolean destroyed;
    //是否是守护线程组
    boolean daemon;
    //虚拟机是否允许暂停
    boolean vmAllowSuspension;
    //未启动线程数量
    int nUnstartedThreads = 0;
    //启动线程数量
    int nthreads;
    //所有线程快照
    Thread threads[];
    //所有子线程组数量
    int ngroups;
    //所有子线程组快照
    ThreadGroup groups[];
```
```
public String toString() {
        return getClass().getName() + "[name=" + getName() + ",maxpri=" + maxPriority + "]";
    }
```
## ThreadGroup构造函数 ##
构造函数需要父线程组和线程组名称，子线程组会继承父线程的一些属性。
```
     //创建一个空的线程组且没有父线程组，创建系统线程组时使用，
    private ThreadGroup() {     // called from C code
        this.name = "system";
        this.maxPriority = Thread.MAX_PRIORITY;
        this.parent = null;
    }

	public ThreadGroup(String name) {
        this(Thread.currentThread().getThreadGroup(), name);
    }
    
    public ThreadGroup(ThreadGroup parent, String name) {
        this(checkParentAccess(parent), parent, name);
    }
    
	//Void unused没有使用，提供一种函数调用位，在对象创建之前执行。
    private ThreadGroup(Void unused, ThreadGroup parent, String name) {
        this.name = name;
        this.maxPriority = parent.maxPriority;
        this.daemon = parent.daemon;
        this.vmAllowSuspension = parent.vmAllowSuspension;
        this.parent = parent;
        parent.add(this);//添加到父线程组
    }
```
```
	public final String getName() {
        return name;
    }
    public final int getMaxPriority() {
        return maxPriority;
    }
    public final boolean isDaemon() {
        return daemon;
    }
    public synchronized boolean isDestroyed() {
        return destroyed;
    }
    public final ThreadGroup getParent() {
        if (parent != null)
            parent.checkAccess();
        return parent;
    }
    
    public final void setDaemon(boolean daemon) {
        checkAccess();
        this.daemon = daemon;
    }
    
    public final void setMaxPriority(int pri) {
        int ngroupsSnapshot;
        ThreadGroup[] groupsSnapshot;
        synchronized (this) {
            checkAccess();
            if (pri < Thread.MIN_PRIORITY || pri > Thread.MAX_PRIORITY) {
                return;
            }
            maxPriority = (parent != null) ? Math.min(pri, parent.maxPriority) : pri;
            ngroupsSnapshot = ngroups;
            if (groups != null) {
                groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
            } else {
                groupsSnapshot = null;
            }
        }
        for (int i = 0 ; i < ngroupsSnapshot ; i++) {
            groupsSnapshot[i].setMaxPriority(pri);
        }
    }
    //测试此线程组是否为线程组参数或其祖先线程组之一
    public final boolean parentOf(ThreadGroup g) {
        for (; g != null ; g = g.parent) {
            if (g == this) {
                return true;
            }
        }
        return false;
    }
    //返回当前线程组及其所有子线程组中活动线程的总数
     public int activeCount() {
        int result;
        int ngroupsSnapshot;//活跃子线程数量快照
        ThreadGroup[] groupsSnapshot;//子线程组快照
        synchronized (this) {
            if (destroyed) {
                return 0;
            }
            result = nthreads;
            ngroupsSnapshot = ngroups;
            if (groups != null) {
            	//活跃线程组复制一份到线程组快照
                groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
            } else {
                groupsSnapshot = null;
            }
        }
        for (int i = 0 ; i < ngroupsSnapshot ; i++) {
            result += groupsSnapshot[i].activeCount();
        }
        return result;
    }
    //返回当前线程组及其所有子线程组中活动线程组的总数
    public int activeGroupCount() {
        int ngroupsSnapshot;
        ThreadGroup[] groupsSnapshot;
        synchronized (this) {
            if (destroyed) {
                return 0;
            }
            ngroupsSnapshot = ngroups;
            if (groups != null) {
                groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
            } else {
                groupsSnapshot = null;
            }
        }
        int n = ngroupsSnapshot;
        for (int i = 0 ; i < ngroupsSnapshot ; i++) {
            n += groupsSnapshot[i].activeGroupCount();
        }
        return n;
    } 
    //中断线程组及子线程组中所有线程
    public final void interrupt() {
        int ngroupsSnapshot;
        ThreadGroup[] groupsSnapshot;
        synchronized (this) {
            checkAccess();
            for (int i = 0 ; i < nthreads ; i++) {
                threads[i].interrupt();
            }
            ngroupsSnapshot = ngroups;
            if (groups != null) {
                groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
            } else {
                groupsSnapshot = null;
            }
        }
        for (int i = 0 ; i < ngroupsSnapshot ; i++) {
            groupsSnapshot[i].interrupt();
        }
    }
    //销毁线程组及其所有子线程组
    public final void destroy() {
        int ngroupsSnapshot;
        ThreadGroup[] groupsSnapshot;
        synchronized (this) {
            checkAccess();
            if (destroyed || (nthreads > 0)) {
                throw new IllegalThreadStateException();
            }
            ngroupsSnapshot = ngroups;
            if (groups != null) {
                groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
            } else {
                groupsSnapshot = null;
            }
            if (parent != null) {
                destroyed = true;
                ngroups = 0;
                groups = null;
                nthreads = 0;
                threads = null;
            }
        }
        for (int i = 0 ; i < ngroupsSnapshot ; i += 1) {
            groupsSnapshot[i].destroy();
        }
        if (parent != null) {
            parent.remove(this);
        }
    }

  //添加一个指定的线程组到当前线程组（添加子线程组）
  private final void add(ThreadGroup g){
        synchronized (this) {
            if (destroyed) {
                throw new IllegalThreadStateException();
            }
            if (groups == null) {
                groups = new ThreadGroup[4];
            } else if (ngroups == groups.length) {
                groups = Arrays.copyOf(groups, ngroups * 2);
            }
            groups[ngroups] = g;
            // This is done last so it doesn't matter in case the
            // thread is killed
            ngroups++;
        }
    }
    //从当前线程组中移除一个指定的子线程组
    private void remove(ThreadGroup g) {
        synchronized (this) {
            if (destroyed) {
                return;
            }
            for (int i = 0 ; i < ngroups ; i++) {
                if (groups[i] == g) {
                    ngroups -= 1;
                    System.arraycopy(groups, i + 1, groups, i, ngroups - i);
                    // Zap dangling reference to the dead group so that
                    // the garbage collector will collect it.
                    groups[ngroups] = null;
                    break;
                }
            }
            if (nthreads == 0) {
                notifyAll();
            }
            if (daemon && (nthreads == 0) &&
                (nUnstartedThreads == 0) && (ngroups == 0))
            {
                destroy();
            }
        }
    }
    //未启动线程加一，创建线程是被调用
    void addUnstarted() {
        synchronized(this) {
            if (destroyed) {
                throw new IllegalThreadStateException();
            }
            nUnstartedThreads++;
        }
    }
    //添加一个线程到当前线程组中，线程启动时调用
    void add(Thread t) {
        synchronized (this) {
            if (destroyed) {
                throw new IllegalThreadStateException();
            }
            if (threads == null) {
                threads = new Thread[4];
            } else if (nthreads == threads.length) {
                threads = Arrays.copyOf(threads, nthreads * 2);
            }
            //添加启动的线程到线程数组
            threads[nthreads] = t;

            // This is done last so it doesn't matter in case the
            // thread is killed
            nthreads++;

            // The thread is now a fully fledged member of the group, even
            // though it may, or may not, have been started yet. It will prevent
            // the group from being destroyed so the unstarted Threads count is
            // decremented.
            nUnstartedThreads--;
        }
    }
    //线程启动失败
    void threadStartFailed(Thread t) {
        synchronized(this) {
            remove(t);
            nUnstartedThreads++;
        }
    }
    //线程结束操作
	void threadTerminated(Thread t) {
        synchronized (this) {
            remove(t);
            if (nthreads == 0) {
                notifyAll();
            }
            if (daemon && (nthreads == 0) &&
                (nUnstartedThreads == 0) && (ngroups == 0))
            {
                destroy();
            }
        }
    }
   //从当前线程组中移除一个指定的子线程，未启动线程数量加一，活动线程数量减一
    private void remove(Thread t) {
        synchronized (this) {
            if (destroyed) {
                return;
            }
            for (int i = 0 ; i < nthreads ; i++) {
                if (threads[i] == t) {
                    System.arraycopy(threads, i + 1, threads, i, --nthreads - i);
                    // Zap dangling reference to the dead thread so that
                    // the garbage collector will collect it.
                    threads[nthreads] = null;
                    break;
                }
            }
        }
    }
```

```
	//检测当前运行的线程是否有权修改此线程组
	public final void checkAccess() {
        SecurityManager security = System.getSecurityManager();
        if (security != null) {
            security.checkAccess(this);
        }
    }
    //检测当前运行的线程是否有权修改此线程组的父线程组
	private static Void checkParentAccess(ThreadGroup parent) {
        parent.checkAccess();
        return null;
    }
    private int enumerate(Thread list[], int n, boolean recurse) {
        int ngroupsSnapshot = 0;
        ThreadGroup[] groupsSnapshot = null;
        synchronized (this) {
            if (destroyed) {
                return 0;
            }
            int nt = nthreads;
            if (nt > list.length - n) {
                nt = list.length - n;
            }
            for (int i = 0; i < nt; i++) {
                if (threads[i].isAlive()) {
                    list[n++] = threads[i];
                }
            }
            if (recurse) {
                ngroupsSnapshot = ngroups;
                if (groups != null) {
                    groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
                } else {
                    groupsSnapshot = null;
                }
            }
        }
        if (recurse) {
            for (int i = 0 ; i < ngroupsSnapshot ; i++) {
                n = groupsSnapshot[i].enumerate(list, n, true);
            }
        }
        return n;
    }
```



