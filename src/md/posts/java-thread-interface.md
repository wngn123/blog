<!--
author: wngn123
head: head.png
date: 2016-08-31
title: Java线程 Runnable和Callable
tags: java
category: Java
status: publish
summary: 阅读Java的源码，Java线程
-->

## Java线程 ##


在Java中，开启一个线程的唯一方式是，是通过Thread的start方法，并且在线程中执行的Runnable的run方法。无论是线程池还是Callable，Future,最核心最根本的还是调用到`Thread.start()–>Runnable.run()`，其他的类的出现可以认为是更方便的使用Thread和Runnable，以此为核心会更容易理解Java的并发框架。

虽然Thread和Runnable类使得多线程编程简单直接，但有一个缺陷就是：在执行完任务之后无法获取执行结果。如果需要获取执行结果，就必须通过共享变量或者使用线程通信的方式来达到效果，这样使用起来就比较麻烦。

## 线程接口Runnable和Callable ##
Callable接口和Runnable接口相似，区别就是Callable需要实现call方法，而Runnable需要实现run方法；并且，call方法还可以返回任何对象，无论是什么对象，JVM都会当作Object来处理。但是如果使用了泛型，我们就不用每次都对Object进行转换了，Callable能够抛出exception,而Runnable不可以。

Runnable
```
public interface Runnable {
    public abstract void run();
}
```
Callable
```
public interface Callable<V> {
    V call() throws Exception;
}
```
## Runnable和Thread ##
Thread实现了Runnable接口（可以认为Thread是Runnable的子类）

```
public class Thread implements Runnable 
```

```
	public static void main(String[] args) {
        Thread t = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("--------");
            }
        });
        t.start();
    }
```

## Callable和Future ##

```
	public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        Future<Integer> result = executor.submit(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                System.out.println("--------");
                Thread.sleep(3000);
                int sum = 0;
                for (int i = 0; i < 100; i++)
                    sum += i;
                return sum;
            }
        });
        executor.shutdown();
        try {
            System.out.println("task运行结果" + result.get());
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("所有任务执行完毕");
    }
```

```
//执行已提交的 Runnable 任务的对象。此接口提供一种将任务提交与每个任务将如何运行的机制（包括线程使用的细节、调度等）分离开来的方法
public interface Executor {
	//在未来某个时间执行给定的命令。该命令可能在新的线程、已入池的线程或者正调用的线程中执行，这由 Executor 实现决定。
    void execute(Runnable command);
}
```