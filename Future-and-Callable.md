title: Future and Callable
date: 2016-11-26 22:44:35
categories:
tags:
---
1. Callable和Runnable的区别？
1) Callable接口的call()方法有返回值，而Runnable接口的run()方法没有返回值
2) Callable接口的call()方法可以声明抛出异常，而Runnable接口的run()方法不可以声明抛出异常

get()方法阻塞性
```
public class MyCallable implements Callable<String> {
    private int age;

    public MyCallable(int age) {
        this.age = age;
    }

    @Override
    public String call() throws Exception {
        Thread.sleep(3000);
        return "return value is " + age;
    }
}

public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyCallable callable = new MyCallable(100);
        ExecutorService executor = Executors.newCachedThreadPool();
        Future<String> future = executor.submit(callable);
        System.out.println("main A");
        System.out.println(future.get());
        System.out.println("main B");
    }
}
```
submit()不仅可以传入Callable对象，还可以传入Runnable对象，说明submit()方法支持有返回值和五返回值的功能。
```
public class MyRunnable implements Runnable {
    private UserInfo userInfo;
    public MyRunnable(UserInfo userInfo){
        this.userInfo = userInfo;
    }
    @Override
    public void run() {
        userInfo.setUsername("usernameValue");
        userInfo.setPassword("passwordValue");
    }
}

public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        UserInfo userInfo = new UserInfo();
        MyRunnable myRunnable = new MyRunnable(userInfo);
        ThreadPoolExecutor pool = new ThreadPoolExecutor(10,10,10,TimeUnit.SECONDS,new LinkedBlockingDeque<>());
        Future<UserInfo> future = pool.submit(myRunnable, userInfo);
        System.out.println("begin time " + System.currentTimeMillis());
        userInfo = future.get();
        System.out.println(userInfo.getUsername()+" " + userInfo.getPassword());
    }
}

```
### Future缺点
使用线程池时，默认情况下也是使用FutureTask作为接口Future的实现类，也就是说在使用Future和Callable的情况下，使用Future就是在使用FutureTask类。
```
public class FutureTask<V> implements RunnableFuture<V>
public interface RunnableFuture<V> extends Runnable, Future<V>
```
Future接口调用get()是阻塞性的，一直阻塞到此任务完成为止。
```
public class RunTest {
    public static void main(String[] args){
        MyCallable callable1 = new MyCallable("username1", 5000);
        MyCallable callable2 = new MyCallable("username2", 4000);
        MyCallable callable3 = new MyCallable("username3", 3000);
        MyCallable callable4 = new MyCallable("username4", 2000);
        MyCallable callable5 = new MyCallable("username5", 1000);
        ArrayList<Callable> callableList = new ArrayList<Callable>();
        callableList.add(callable1);
        callableList.add(callable2);
        callableList.add(callable3);
        callableList.add(callable4);
        callableList.add(callable5);
        List<Future> futureList = new ArrayList<Future>();
        ThreadPoolExecutor executor = new ThreadPoolExecutor(5, 10, 5, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        for(int i=0;i<5;i++){
            futureList.add(executor.submit(callableList.get(i)));
        }
        System.out.println("run first time " + System.currentTimeMillis());
        for(int i=0;i<5;i++){
            try {
                // 一个future对应一个指定的callable，按顺序的
                System.out.println(futureList.get(i).get()+" " + System.currentTimeMillis());
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }
    }
}
```

