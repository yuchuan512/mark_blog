title: Executor与ThreadPoolExecutor
date: 2016-11-26 18:31:52
categories:
tags:
---
### 创建线程池
 * 创建无界线程池
```
ExecutorService executor = Executors.newCachedThreadPool();
```
 * 创建固定数量的线程池
```
ExecutorService executorService = Executors.newFixedThreadPool(3);
```
 * 创建单一线程的线程池
```
ExecutorService executorService = Executors.newSingleThreadExecutor();
```
 * 创建定时或周期任务的线程池
```
ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(3);
```

### 使用Executors创建线程池
一般使用Executors的静态方法创建线程池，需要定制化可以使用ThreadPoolExecutor类进行详细参数设定。
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();
        for(int i=0;i<5;i++){
            final int k = i;
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName()+" user"+(k+1));
                }
            });
        }
        Thread.sleep(3000);
        for(int i=0;i<5;i++){
            final int k = i;
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName()+" user"+(k+1));
                }
            });
        }
    }
}

```
可以观察到使用newCachedThreadPool创建的线程池里面的线程得到了复用，因为线程池线程名称唯一（从源码可以看出默认新创建的线程会保持60秒)
**使用Executors的静态方法底层是调用了ThreadPoolExecutor类**
```
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

### 使用newCachedThreadPool(ThreadFactory) 替换默认线程工厂
```
public class MyThreadFactory implements ThreadFactory{
    @Override
    public Thread newThread(Runnable r) {
        Random random = new Random();
        Thread thread = new Thread(r);
        thread.setName("yuchuan-thread-" + random.nextInt(100));
        return thread;
    }
}

public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyThreadFactory myThreadFactory = new MyThreadFactory();
        ExecutorService executorService = Executors.newCachedThreadPool(myThreadFactory);
        executorService.execute(new Runnable() {
            @Override
            public void run() {
                System.out.println("running " + Thread.currentThread().getName());
            }
        });
    }
}
```

ThreadPoolExecutor 使用Executors的静态方法底层都是调用了ThreadPoolExecutor类 
```
ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) 
```

### 线程池核心数量与任务数关系
如果线程数量 < corePoolSize 则直接执行任务，不放入扩展队列Queue中。
```
ThreadPoolExecutor executor = new ThreadPoolExecutor(7,8,5,TimeUnit.SECONDS,new LinkedBlockingDeque<Runnable>())
```
如果corePoolSize < 线程数量 < maximumPoolSize且队列使用LinkedBlockingDeque，则放入队列；如果队列使用SynchronousQueue类，会立即执行，且当时间keepAliveTime超过5秒时，清除空闲线程。
BlockingQueue 常用的实现类LinkedBlockingQueue 和ArrayBlockingQueue。用 LinkedBlockingQueue的好处在于没有大小限制，所以执行execute()不会抛出异常。线程池中运行的线程数也永远不会超过corePoolSize，因为其他多于的线程被放入LinkedBlockingQueue队列，keepAliveTime参数也就没有意义。

如果线程数量 > maximumPoolSize 且队列使用 LinkedBlockingQueue 则会放入队列；若队列使用 SynchronousQueue 则会抛出拒绝异常

### shutdown() VS shutdownNow()
> 方法shutdown()的作用是使当前未执行完的线程继续执行，而不再添加新的任务task，还有shutdown()方法不会阻塞，调用shutdown()方法后，主线程main就马上结束了，而线程池会继续运行直到所有任务执行完才会停止。如果不调用shutdown()方法，那么线程池会一直保持下去，以便随时执行被添加的task任务。
> 方法shutdownNow()的作用是中断所有的任务task，并且抛出InterruptedException异常，前提是在Runnable中使用if(Thread.currentThread.isInterrupted()==true)语句来判断当前线程的中断状态，而未执行的线程不再执行，也就是从执行队列中清除。如果没有if(Thread.currentThread.isInterrupted()==true)语句及抛出异常的代码，则池中正在运行的线程直到执行完毕，而未执行的线程将不再执行，也从执行队列中清除。
```
public class MyRunnable1 implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+" " + System.currentTimeMillis());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+" end");
    }
}

public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyRunnable1 myRunnable1 = new MyRunnable1();
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 10, 10, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        pool.execute(myRunnable1);
        pool.execute(myRunnable1);
        pool.execute(myRunnable1);
        pool.execute(myRunnable1);
        Thread.sleep(1000);
        pool.shutdown();
        pool.execute(myRunnable1);
        System.out.println("main end!");
    }
}

```
结果线程池执行shutdown()关闭之后，状态变为SHUTDOWN状态。此时如果再添加任务会抛出RejectedExecutionException异常，但线程池中的任务不会立刻退出，直到任务处理完成，线程池退出。说明 shutdown 关闭线程池之后，线程（正在执行或是在队列中）还会正常执行

shutdownNow()方法是使线程池的状态变为STOP状态，并试图停止所有正在执行的线程（如果有if判断则认为的抛出异常），不在处理还在队列中等待的任务，可以通过List<Runnable> list = pool.shutdownNow()返回那些未执行的任务。
```
public class MyRunnable1 implements Runnable {
    @Override
    public void run() {
        try{
            for(int i=0;i<Integer.MAX_VALUE/50;i++){
                Math.random();
                Math.random();
                Math.random();
                Math.random();
                // 被中断线程时候抛出异常并执行异常操作
                if(Thread.currentThread().isInterrupted()==true){
                    System.out.println("is interrupted!");
                    throw new InterruptedException();
                }
            }
            System.out.println("complete!");
        }catch (InterruptedException e){
            System.out.println("catch interrupted!");
            e.printStackTrace();
        }
    }
}

public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyRunnable1 myRunnable1 = new MyRunnable1();
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 10, 10, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        pool.execute(myRunnable1);
        pool.execute(myRunnable1);
        pool.execute(myRunnable1);
        pool.execute(myRunnable1);
        Thread.sleep(1000);
        // 执行shutdownNow()可以返回List<Runnable>,存储的是未运行的任务
        List<Runnable> list = pool.shutdownNow();
        System.out.println("main end! " + list.size());
    }
}
```
使用isShutdown()判断线程池是否关闭,判断这个命令发出或未发出。
pool.isTerminating()和pool.isTerminated()判断线程池正在停止还是已停止状态
如果线程池关闭后，也就是所有任务都已完成，则方法isTerminated()返回true
awaitTermination(long timeout, TimeUnit unit)作用就是查看在制定的时间之间，线程池是否已经终止工作，也就是最多等待多少时间去判断线程是否已经终止工作，此方法需要**shutdown()**方法的配合
```
public class MyRunnable1 implements Runnable {
    @Override
    public void run() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyRunnable1 myRunnable = new MyRunnable1();
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 10, 10, TimeUnit.SECONDS, new LinkedBlockingDeque<Runnable>());
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.shutdown();
        System.out.println("main begin");
        System.out.println(pool.awaitTermination(1, TimeUnit.SECONDS));
        System.out.println(pool.awaitTermination(1, TimeUnit.SECONDS));
        System.out.println(pool.awaitTermination(1, TimeUnit.SECONDS));
        System.out.println(pool.awaitTermination(1, TimeUnit.SECONDS));
        System.out.println(pool.awaitTermination(1, TimeUnit.SECONDS));
        System.out.println("main end");
    }
}
结果 
main begin
false
false
false
true
true
main end
```
方法awaitTermination()与shutdown()结合可以实现**等待执行完毕**的效果。原理就是应用awaitTermination()方法具有阻塞性，如果awaitTermination()方法正在阻塞的过程中任务执行完毕，则awaitTerminal()取消阻塞继续执行后面的代码，如下所示。
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("begin");
        MyRunnable1 myRunnable = new MyRunnable1();
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 10, 10, TimeUnit.SECONDS, new LinkedBlockingDeque<Runnable>());
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.shutdown();
        System.out.println(pool.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS));
        System.out.println("main end");
    }
}
```

### ThreadFactory+execute()+UncaughtExceptionHandler处理异常
ThreadPoolExecutor通过设置ThreadFactory，实现UncaughtExceptionHandler来处理异常
```
public class MyRunnable1 implements Runnable {
    @Override
    public void run() {
        int a = 5/0;
    }
}

public class MyThreadFactoryB implements ThreadFactory {
    @Override
    public Thread newThread(Runnable r) {
        Thread thread = new Thread(r);
        thread.setName("my name ");
        thread.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
            @Override
            public void uncaughtException(Thread t, Throwable e) {
                System.out.println("自定义处理异常 " +t.getName()+" " + e.getMessage());
                e.printStackTrace();
            }
        });
        return thread;
    }
}

public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyRunnable1 myRunnable = new MyRunnable1();
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 10, 10, TimeUnit.SECONDS, new LinkedBlockingDeque<Runnable>());
        pool.setThreadFactory(new MyThreadFactoryB());
        pool.execute(myRunnable);
    }
}
```

### set/getRejectedExcutionHandler()
实现RejectedExecutionHandler接口来处理任务被拒绝执行时的行为。
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyRunnable1 one = new MyRunnable1("one");
        MyRunnable1 two = new MyRunnable1("two");
        MyRunnable1 three = new MyRunnable1("three");
        MyRunnable1 four = new MyRunnable1("four");
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 3, 10, TimeUnit.SECONDS,
                new SynchronousQueue<Runnable>());
        // 匿名函数处理拒绝执行行为
        pool.setRejectedExecutionHandler(new RejectedExecutionHandler() {
            @Override
            public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
                System.out.println(((MyRunnable1)r).getUsername()+" is rejected!");
            }
        });
        pool.execute(one);
        pool.execute(two);
        pool.execute(three);
        pool.execute(four);
    }
}
结果
four is rejected!
pool-1-thread-1 begin 1480148850840
pool-1-thread-2 begin 1480148850840
pool-1-thread-3 begin 1480148850840
pool-1-thread-2 end 1480148853845
pool-1-thread-3 end 1480148853845
pool-1-thread-1 end 1480148853845
```
allowsCoreThreadTimeOut()(boolean) 配置核心线程是否超时，超时则和其他线程一样清除

### getCompletedTaskCount()
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(1000);
                    System.out.println(Thread.currentThread().getName() + " print ");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 3, 5, TimeUnit.SECONDS,
                new LinkedBlockingDeque<Runnable>());
        pool.execute(runnable);
        pool.execute(runnable);
        pool.execute(runnable);
        pool.execute(runnable);
        pool.execute(runnable);
        Thread.sleep(1000);
        System.out.println(pool.getCompletedTaskCount());
        Thread.sleep(1000);
        System.out.println(pool.getCompletedTaskCount());
        Thread.sleep(1000);
        System.out.println(pool.getCompletedTaskCount());
        Thread.sleep(1000);
        System.out.println(pool.getCompletedTaskCount());
    }
}
```

### 线程池ThreadPoolExecutor的拒绝策略
线程池中的资源全部被占用的时候，读新添加的Task任务有不同的处理策略，在默认情况下，有四种处理方式：
1. AbortPolicy：当任务添加到线程池被拒绝时，抛出RejectExecutionException异常
2. CallerRunsPolicy：使用调用线程池的Thread线程对象处理被拒绝的任务
3. DiscardOldestPolicy：线程池会放弃等待队列中最旧的未处理任务，然后将被拒绝的任务添加到等待队列中
4. DiscardPolicy：线程池将丢弃被拒绝的任务

 * AbortPolicy
```
ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 3, 5, TimeUnit.SECONDS, queue,
                new ThreadPoolExecutor.AbortPolicy());
```
 * CallerRunsPolicy
由调用者处理被拒绝的任务
```
MyRunnable2 myRunnable = new MyRunnable2();
        LinkedBlockingDeque<Runnable> queue = new LinkedBlockingDeque<>(2);
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 3, 5, TimeUnit.SECONDS, queue,
                new ThreadPoolExecutor.CallerRunsPolicy());
        System.out.println("a begin ");
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        pool.execute(myRunnable);
        System.out.println("a end");
        pool.shutdown();
        pool.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
结果
a begin 
end main
end pool-1-thread-3
end pool-1-thread-1
end pool-1-thread-2
end main
a end
end pool-1-thread-3
end pool-1-thread-1
```
 * DiscardOldestPolicy
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        ArrayBlockingQueue queue = new ArrayBlockingQueue(2);
        ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 3, 5, TimeUnit.SECONDS, queue,
                new ThreadPoolExecutor.DiscardOldestPolicy());
        for(int i=0;i<5;i++){
            MyRunnable myRunnable = new MyRunnable("Runnable"+(i+1));
            pool.execute(myRunnable);
        }
        Thread.sleep(500);
        Iterator iterator = queue.iterator();
        while(iterator.hasNext()){
            MyRunnable mr = (MyRunnable) iterator.next();
            System.out.println(mr.getUsername());
        }
        pool.execute(new MyRunnable("Runnable6"));
        pool.execute(new MyRunnable("Runnable7"));
        iterator = queue.iterator();
        while(iterator.hasNext()){
            MyRunnable mr2 = (MyRunnable) iterator.next();
            System.out.println(mr2.getUsername());
        }
        pool.shutdown();
        pool.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
    }
结果
Runnable1 run
Runnable5 run
Runnable2 run
Runnable3
Runnable4
Runnable6
Runnable7
Runnable6 run
Runnable7 run
}
```
 * DiscardPolicy
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        MyRunnable2 myrunnable = new MyRunnable2();
        ArrayBlockingQueue<Runnable> queue = new ArrayBlockingQueue<>(2);
        ThreadPoolExecutor executor = new ThreadPoolExecutor(2, 3, 5, TimeUnit.SECONDS, queue, new ThreadPoolExecutor.DiscardPolicy());
        executor.execute(myrunnable);
        executor.execute(myrunnable);
        executor.execute(myrunnable);
        executor.execute(myrunnable);
        executor.execute(myrunnable);
        executor.execute(myrunnable);
        executor.execute(myrunnable);
        Thread.sleep(1000);
        executor.shutdown();
        executor.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
    }
}
结果
end pool-1-thread-1
end pool-1-thread-3
end pool-1-thread-2
end pool-1-thread-1
end pool-1-thread-3
```

### afterExecute()和beforeExecute()
重写这两个方法可以对线程池中执行的线程对象实施监控。
```
public class MyThreadPoolExecutor extends ThreadPoolExecutor {
    public MyThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue);
    }

    @Override
    protected void afterExecute(Runnable r, Throwable t) {
        super.afterExecute(r, t);
        System.out.println(((MyRunnable)r).getUsername()+" execute finish!");
    }

    @Override
    protected void beforeExecute(Thread t, Runnable r) {
        super.beforeExecute(t, r);
        System.out.println(((MyRunnable)r).getUsername()+" prepare to execute!");
    }
}

public static void main(String[] args) throws InterruptedException {
        MyThreadPoolExecutor executor = new MyThreadPoolExecutor(2, 2, Integer.MAX_VALUE, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        executor.execute(new MyRunnable("thread1"));
        executor.execute(new MyRunnable("thread2"));
        executor.execute(new MyRunnable("thread3"));
        executor.execute(new MyRunnable("thread4"));
    }
```

ExecutorService可以使用remove方法移除未被执行的任务，前提是任务是使用execute()方式提交的；如果使用submit提交的任务，未被执行也不能删除此任务。

ExecutorService中submit和execute的区别
 * 接受的参数不一样
ExecutorService中的方法
```
1) Future<?> submit(Runnable task);
2) <T> Future<T> submit(Runnable task, T result);
3) <T> Future<T> submit(Callable<T> task);
```
Executor 接口唯一方法
```
void execute(Runnable command);
```
 * submit有返回值，而execute没有
 * submit方便Exception处理
submit可以直接捕获异常，通过catch ExecutionException的方式
```
public class RunTest {
    public static void main(String[] args){
        ThreadPoolExecutor executor = new ThreadPoolExecutor(50, 50, 5, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        Future future = executor.submit(new Callable<String>() {
            @Override
            public String call() throws Exception {
                Integer.parseInt("a");
                return "我是返回值";
            }
        });
        try {
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            System.out.println("捕获到异常");
            e.printStackTrace();
        }
    }
}
```
execute捕获异常需要通过自定义ThreadFactory的方式进行捕获
```
public class RunTest {
    public static void main(String[] args){
        ThreadPoolExecutor executor = new ThreadPoolExecutor(50, 50, 5, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        executor.setThreadFactory(new ThreadFactory() {
            @Override
            public Thread newThread(Runnable r) {
                Thread t = new Thread(r);
                t.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
                    @Override
                    public void uncaughtException(Thread t, Throwable e) {
                        System.out.println("execute()使用自定义方法捕获异常");
                        e.printStackTrace();
                    }
                });
                return t;
            }
        });
        executor.execute(new Runnable() {
            @Override
            public void run() {
                Integer.parseInt("a");
            }
        });
    }
}
```
Test
```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService pool = Executors.newFixedThreadPool(2);
        // execute方式
        pool.execute(new RunnableTest("Task1"));
        // submit方式
        Future<?> future = pool.submit(new RunnableTest("Task2"));
        try{
            if(future.get()==null){
                System.out.println("任务完成");
            }
        } catch (ExecutionException e) {
            System.out.println(e.getCause().getMessage());
            e.printStackTrace();
        }
    }
    static class RunnableTest implements Runnable{
        private String taskName;
        public RunnableTest(String taskName){
            this.taskName = taskName;
        }
        @Override
        public void run() {
            System.out.println("inside "+taskName);
            throw new RuntimeException("Runtime Exception from inside " + taskName);
        }
    }
}
```
### get方法测试
getActiveCount()正在执行任务的线程
getPoolSize() 线程池中总共的线程
getCompletedTaskCount()  已经任务执行完毕的线程

```
public class ExecutorTest {
    public static void main(String[] args) throws InterruptedException {
        try{
            MyRunnable2 myrunnable = new MyRunnable2();
            SynchronousQueue<Runnable> queue = new SynchronousQueue<>();
            ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 5, 5, TimeUnit.SECONDS, queue);
            pool.execute(myrunnable);
            pool.execute(myrunnable);
            pool.execute(myrunnable);
            System.out.println(pool.getActiveCount()+"  " + pool.getPoolSize());
            Thread.sleep(7000);
            System.out.println(pool.getActiveCount()+"  " + pool.getPoolSize());
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

### 自定义拒绝策略
接口RejectedExecutionHandler的主要作用是当线程池关闭后依然有任务要执行时，可以实现一些处理
```
public class RunTest {
    public static void main(String[] args){
        ExecutorService service = Executors.newCachedThreadPool();
        ThreadPoolExecutor executor = (ThreadPoolExecutor)service;
        executor.setRejectedExecutionHandler(new RejectedExecutionHandler() {
            @Override
            public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
                System.out.println(((FutureTask)r).toString()+" is rejected!");
            }
        });
        service.submit(new MyRunnable("A"));
        service.submit(new MyRunnable("B"));
        service.submit(new MyRunnable("C"));
        executor.shutdown();
        service.submit(new MyRunnable("D"));
    }
}
```



