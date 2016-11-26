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

