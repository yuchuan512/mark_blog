title: ScheduledExecutorService
date: 2016-11-28 22:33:38
categories:
tags:
---
### ScheduleExecutorService
java中的计划任务Timer工具类提供了以计时器或计划任务的功能来实现按指定时间或时间间隔执行任务，但**由于Timer不是以池pool，而是以队列的方式来管理线程的，所以在高并发的情况下运行效率较低**，在新版JDK中提供了ScheduledExecutorService对象来解决效率与定时任务的功能
**ScheduledExecutorService 的主要作用就是可以将定时任务与线程池功能结合使用**
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ArrayList<Callable> callableList = new ArrayList<>();
        callableList.add(new MyCallableA());
        callableList.add(new MyCallableB());
        ScheduledExecutorService executor = Executors.newSingleThreadScheduledExecutor();
        ScheduledFuture futureA = executor.schedule(callableList.get(0), 4, TimeUnit.SECONDS);
        ScheduledFuture futureB = executor.schedule(callableList.get(1), 4, TimeUnit.SECONDS);
        long start = System.currentTimeMillis();
        System.out.println(futureA.get()+" " + futureB.get());
        long end = System.currentTimeMillis();
        System.out.println("time " + (end-start)/1000);
    }
}
结果
callA begin 
callA end
callB begin 
callB end
returnA returnB
time 7
```
默认是单任务的，等待的时间是同时在消耗的，所以第一个任务4s时候执行，执行完后，第二个任务立马执行。源码
```
public static ScheduledExecutorService newSingleThreadScheduledExecutor() {
	 // 默认产生一个线程来执行任务
    return new DelegatedScheduledExecutorService(new ScheduledThreadPoolExecutor(1));
    }
```
改为多任务即线程池类型
```
ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
```
源码
```
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }
```

**ScheduledThreadPoolExecutor 实现了ScheduledExecutorService接口**
### 使用Runnable延迟执行
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ArrayList<Callable> callableList = new ArrayList<>();
        ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
        executor.schedule(new MyRunnable(), 1, TimeUnit.SECONDS);
    }
}
```
### 使用Callable延迟运行并取得返回值
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
        ScheduledFuture futureA = executor.schedule(new MyCallableA(), 0, TimeUnit.SECONDS);
        long start = System.currentTimeMillis();
        System.out.println(futureA.get());
        long end = System.currentTimeMillis();
        System.out.println("time " + (end-start)/1000);
    }
}
```
### ScheduleAtFixedRate 实现周期性执行
若执行时间大于周期时间，相当于每次都按照周期时间叠加，上一次与下一次开始时间相差实际运行的时间而不是周期时间，下一次开始会紧接着上一次结束开始；
例如,任务执行4s，周期2s。
```
Running ... begin 1480335883 s
Running ... enden 1480335887 s
Running ... begin 1480335887 s
Running ... enden 1480335891 s
Running ... begin 1480335891 s
Running ... enden 1480335895 s
Running ... begin 1480335895 s
Running ... enden 1480335899 s
Running ... begin 1480335899 s
```
若执行时间小于周期时间，则每次执行完会间隔(周期时间-任务执行时间)，才开始下一次任务的执行。
```
Running ... begin 1480335930 s
Running ... enden 1480335931 s
Running ... begin 1480335932 s
Running ... enden 1480335933 s
Running ... begin 1480335934 s
Running ... enden 1480335935 s
Running ... begin 1480335936 s
Running ... enden 1480335937 s
Running ... begin 1480335938 s
Running ... enden 1480335939 s
```
### ScheduleWithFixedDelay() 实现周期性执行
不管执行时间大于或者小于周期时间，都会执行完任务，间隔一个周期时间，再执行下一个任务。相当于两个任务之间有一个固定的间隔时间

### getQueue()与remove()
获取队列中任务（未运行的任务）
```
BlockingQueue<Runnable> queue = executor.getQueue();
Iterator<Runnable> iterator = queue.iterator();
while(iterator.hasNext()){
	Runnable runnable = (Runnable)iterator.next()
}
```
移除队列中未运行的任务
```
ScheduleFuture future2 = executor.scheduleAtFixedRate(runnable,10,2,TimeUnit.SECOND);
executor.remove((Runnable)future2)
```
### setExecuteExistingDelayedTasksAfterShutdownPolicy 
线程池关闭是否停止任务执行
```
ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
        System.out.println("main begin " + System.currentTimeMillis());
        executor.schedule(new MyRunnable("A"),3,TimeUnit.SECONDS);
        // 若为false，则线程池关闭，调度的任务不再继续；默认为true
        executor.setExecuteExistingDelayedTasksAfterShutdownPolicy(false);
        executor.shutdown();
        System.out.println("main ended "+System.currentTimeMillis());
```
### setContinueExistingPeriodicTasksAfterShutdownPolicy
线程池关闭是否停止固定周期或固定延迟的任务
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
        System.out.println("main begin " + System.currentTimeMillis());
        executor.scheduleWithFixedDelay(new MyRunnable("A"),1,2,TimeUnit.SECONDS);
        // 若为false，则线程池关闭，scheduleWithFixedDelay或scheduleAtFixedRate
        // 的任务都关闭；默认为false
        executor.setContinueExistingPeriodicTasksAfterShutdownPolicy(true);
        executor.shutdown();
        System.out.println("main ended "+System.currentTimeMillis());
    }
}
```

### 取消队列中的任务
```
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        try {
            while(true){
                if(Thread.currentThread().isInterrupted()==true){
                    throw new InterruptedException();
                }
                System.out.println("run.. " + userName +" " + System.currentTimeMillis());
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println("中断了");
        }
    }
}

public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
        System.out.println("main begin " + System.currentTimeMillis());
        ScheduledFuture<?> future = executor.schedule(new MyRunnable("A"), 1, TimeUnit.SECONDS);
        // 取消队列中未执行的任务，任务不执行，但不会从队列中删除任务
        System.out.println(future.cancel(true));
        BlockingQueue<Runnable> queue = executor.getQueue();
        Iterator<Runnable> iterator = queue.iterator();
        while(iterator.hasNext()){
            Runnable runnable = (Runnable)iterator.next();
            System.out.println("队列中..."+runnable);
        }
        System.out.println("main ended "+System.currentTimeMillis());
    }
结果
main begin 1480338668742
true
队列中...java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask@677327b6
main ended 1480338668758
```

### 取消队列中正在执行的任务
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
        System.out.println("main begin " + System.currentTimeMillis());
        ScheduledFuture<?> future = executor.schedule(new MyRunnable("A"), 1, TimeUnit.SECONDS);
        Thread.sleep(2000);
        System.out.println(future.cancel(true));
        BlockingQueue<Runnable> queue = executor.getQueue();
        Iterator<Runnable> iterator = queue.iterator();
        while(iterator.hasNext()){
            Runnable runnable = (Runnable)iterator.next();
            System.out.println("队列中..."+runnable);
        }
        System.out.println("main ended "+System.currentTimeMillis());
    }
}
结果
run.. A 1480338842708
run.. A 1480338842708
true
java.lang.InterruptedException
	at com.MyRunnable.run(MyRunnable.java:26)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
main ended 1480338842708
中断了
```

### 说明
1. 在队列中的任务可以取消，任务也不再运行
2. 正在运行的任务可以停止，但要结合 if(Thread.currentThread.isInterrupted()==true)判断

使用 executor.setRemoveOnCancelPolicy(true)配合future.cancel(true)，在取消任务运行的时候，将任务从队列中删除。

### 在一个线程中中断另一个睡眠的线程
currentThread().是静态方法，也就是说 Thread.currentThread().interrupt()和 rThread.currentThread().interrupt();调用的都是vmThread对象。中断睡眠的就是主线程的那个睡眠，rThread.interrupt();就是直指rThread这个线程，中断的也是它。
源码如下：
```
public static Thread currentThread() {
        return VMThread.currentThread();
    }

public void interrupt() {
        checkAccess();
        if (interruptAction != null) {
            interruptAction.run();
        }
        VMThread vmt = this.vmThread;
        if (vmt != null) {
            vmt.interrupt();
        }
    }

```



