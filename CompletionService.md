title: CompletionService
date: 2016-11-28 22:33:47
categories:
tags:
---
### CompletionService
接口CompletionService 的功能是以异步的方式一边生产新的任务，一边处理已完成任务的结果，这样可以将执行任务与从处理任务分离开来进行处理。使用submit执行任务，使用take取得已完成的任务，并按照完成这些任务的时间处理它们的结果。

接口CompletionService仅有一个实现类 ExecutorCompletionService,构造方法
```
public ExecutorCompletionService(Executor executor)
public ExecutorCompletionService(Executor executor,
                                     BlockingQueue<Future<V>> completionQueue)
```
需要依赖于Executor对象，大部分的实现也就是使用线程池ThreadPoolExecutor对象。

Future具有阻塞同步性，接口CompletionService可以解决解决这个问题
```
public class MyCallable implements Callable<String> {
    private String userName;
    private long sleepValue;

    public MyCallable(String userName, long sleepValue) {
        this.userName = userName;
        this.sleepValue = sleepValue;
    }

    @Override
    public String call() throws Exception {
        Thread.sleep(sleepValue);
        return "return " + userName;
    }
}

public class RunTest {
    public static void main(String[] args){
        MyCallable callable1 = new MyCallable("username1", 5000);
        MyCallable callable2 = new MyCallable("username2", 4000);
        MyCallable callable3 = new MyCallable("username3", 3000);
        MyCallable callable4 = new MyCallable("username4", 2000);
        MyCallable callable5 = new MyCallable("username5", 1000);
        List<Callable> callableList = new ArrayList<Callable>();
        callableList.add(callable1);
        callableList.add(callable2);
        callableList.add(callable3);
        callableList.add(callable4);
        callableList.add(callable5);
        ThreadPoolExecutor executor = new ThreadPoolExecutor(5, 10, 5, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        ExecutorCompletionService<Object> csRef = new ExecutorCompletionService<>(executor);
        for(int i=0;i<5;i++){
            csRef.submit(callableList.get(i));
        }
        try {
            for(int i=0;i<5;i++){
                System.out.println("wait for print " + (i+1) + " result");
                System.out.println(csRef.take().get());
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
        executor.shutdown();
    }

结果
wait for print 1 result
return username5
wait for print 2 result
return username4
wait for print 3 result
return username3
wait for print 4 result
return username2
wait for print 5 result
return username1
```
使用CompletionService接口后，哪个任务先执行完，哪个任务的返回值就先打印。如果当前任务没有执行完，则csRef.take().get()方法还是阻塞。
### take()
方法take()取得最先完成任务的Future对象，谁执行时间最短谁最先返回。
```
public class RunTest {
    public static void main(String[] args){
        ExecutorService executorService = Executors.newCachedThreadPool();
        ExecutorCompletionService<String> csRef = new ExecutorCompletionService<String>(executorService);
        for(int i=0;i<10;i++){
            csRef.submit(new Callable<String>() {
                @Override
                public String call() throws Exception {
                    long sleepValue = (int)(Math.random()*1000);
                    System.out.println("sleep="+sleepValue+" "+ Thread.currentThread().getName());
                    Thread.sleep(sleepValue);
                    return "test " + Thread.currentThread().getName();
                }
            });
        }
        try {
            for(int i=0;i<10;i++){
                System.out.println(csRef.take().get());
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```
### poll()
poll()获取并移除已完成任务的Future，非阻塞，不存在已完成任务则返回null
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();
        ExecutorCompletionService<String> csRef = new ExecutorCompletionService<String>(executorService);
        for (int i = 0; i < 10; i++) {
            csRef.submit(new Callable<String>() {
                @Override
                public String call() throws Exception {
                    Thread.sleep(3000);
                    return "test " + Thread.currentThread().getName();
                }
            });
        }
        System.out.println(csRef.poll());
        Thread.sleep(4000);
        System.out.println(csRef.poll().get());
    }
}
结果
null
test pool-1-thread-1
```

### 统计程序运行时间
```
long start = System.currentTimeMillis();
long end = System.currentTimeMillis();
(end-start)/1000 秒
```
### poll()超时处理
poll(long timeout, TimeUnit unit)
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();
        ExecutorCompletionService<String> csRef = new ExecutorCompletionService<String>(executorService);
        MyCallable callableA = new MyCallable("username1",2000);
        MyCallable callableB = new MyCallable("username2",2000);
        long start = System.currentTimeMillis();
        csRef.submit(callableA);
        csRef.submit(callableB);
        for(int i=0;i<2;i++){
            System.out.println("zzz "+ csRef.poll(6,TimeUnit.SECONDS).get());
        }
        System.out.println("main end!");
        long end = System.currentTimeMillis();
        // 关闭线程池确定任务结束
        executorService.shutdown();
        executorService.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
        System.out.println("time is " + (end-start)/1000);
    }
}

结果
zzz return username2
zzz return username1
main end!
time is 2
```
poll(long timeout,TimeUnit unit)方法是阻塞的
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();
        ExecutorCompletionService<String> csRef = new ExecutorCompletionService<String>(executorService);
        MyCallable callableA = new MyCallable("username1",4000);
        MyCallable callableB = new MyCallable("username2",4000);
        long start = System.currentTimeMillis();
        csRef.submit(callableA);
        csRef.submit(callableB);
        // poll方法在接下来的6秒之内会轮询，直到有数据返回继续向下执行，或者超时向下执行
        System.out.println("zzz "+ csRef.poll(6,TimeUnit.SECONDS).get());
        // 从结果可以看出，poll(long timeout,TimeUnit unit)是方法阻塞的
        System.out.println("hello hello ");
        System.out.println("zzz "+ csRef.poll(2,TimeUnit.SECONDS).get());
        long end = System.currentTimeMillis();
        executorService.shutdown();
        executorService.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
        System.out.println("main end!");
        System.out.println("time is " + (end-start)/1000);
    }
}
结果
zzz return username1
hello hello 
zzz return username2
main end!
time is 4
```
### CompletionService 与 异常
某一个任务失败不会影响这个任务执行之前成功的任务，却会导致之后的任务失败。

Future<V> submit(Runnable task,V result)
```
public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();
        ExecutorCompletionService<UserInfo> csRef = new ExecutorCompletionService<UserInfo>(executorService);
        UserInfo userInfo = new UserInfo();
        userInfo.setUsername("one");
        System.out.println("before " + userInfo.getUsername());
        MyRunnable myRunnable = new MyRunnable(userInfo);
        Future<UserInfo> future = csRef.submit(myRunnable, userInfo);
        System.out.println("after " + future.get().getUsername());
    }
}
结果
before one
running ...
after usernameValue
```












