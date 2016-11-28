title: ExecutorService
date: 2016-11-28 22:33:24
categories:
tags:
---
### invokeAny()和invokeAll()具有阻塞性
在ThreadPoolExecutor中使用ExecutorService中的方法
invokeAny()取得第一个完成任务的结果值，当第一个任务执行完成后，会调用interrupte()方法将其他任务中断，所有这些任务重可以结合 if(Thread.currentThread().isInterrupted()==true)代码来决定任务是否继续运行
invokeAll() 等全部线程任务执行完毕后，取得全部完成任务的结果值。

invokAny()在执行过程中出现两种情况
1. 无if(Thread.currentThread.isInterrupted()) 已经获得结果之后，其他线程继续运行直到完成
2. 有if(Thread.currentThread.isInterrupted()) 已经获得结果之后，其他线程中断处理
```
public class MyCallableB2 implements Callable<String> {
    @Override
    public String call() throws InterruptedException {
        System.out.println("MyCallableB2 begin " + System.currentTimeMillis());
        for(int i=0;i<10;i++){
            if(Thread.currentThread().isInterrupted()==false){
                Math.random();
                Math.random();
                Math.random();
                System.out.println("MyCallableB2 " + (i+1));
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                    /**
                     * 此处涉及到中断sleep状态的线程，需要先让sleep退出阻塞状态
                     * 然后才能检测到isInterrupted信号，从而执行中断处理代码
                     * 参考 http://blog.csdn.net/canot/article/details/51087772
                     * 该方法在县城阻塞时抛出一个中断信号，该信号将被catch语句捕获到，一旦捕获
                     * 到这个信号，线程就提前终结自己的阻塞状态，就能再次运行run方法，然后检测中断标志位
                     * 进行后面的清理工作。
                     */
                    Thread.currentThread().interrupt();
                }
            }else{
                System.out.println("*****抛出异常了******");
                throw new InterruptedException();
            }
            System.out.println(Thread.currentThread().isInterrupted());
        }
        System.out.println("MyCallableB2 end");
        return "MyCallableB2";
    }
}

public class RunTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ArrayList<Callable<String>> list = new ArrayList<>();
        list.add(new MyCallableA());
        list.add(new MyCallableB2());
        ExecutorService executor = Executors.newCachedThreadPool();
        String getValueA = executor.invokeAny(list);
        System.out.println("zzz " + getValueA);
        executor.shutdown();
    }
}
结果
MyCallableB2 6
MyCallableA end 1480324168899
zzz returnA
true
*****抛出异常了******
java.lang.InterruptedException: sleep interrupted
	at java.lang.Thread.sleep(Native Method)
```
### invokeAny异常
 * invokeAny()与执行慢的任务异常，执行慢的任务出现异常时，默认情况下不会再控制台输出异常信息。如果显式使用try...catch语句块则可以自定义捕获异常。加入显式的try-catch语句块可以捕获异常信息，但抛出的异常在main方法中没有捕获到，说明子线程出现异常是不影响main线程的主线程的。
 * invokeAny()与执行快的任务异常，先出现异常而不影响后面任务的取值的原理是在源代码中一直判断有没有正确的返回值，如果直到最后都没有获得返回值则抛出异常，这个异常是最后出现的异常。比如A，B，C三个任务一起执行，都出现了异常，则最终的异常就是最后出现的异常。
 * invokeAny()出现异常返回最后一个异常并输出。

### invokeAny超时处理
invokeAny(Collection tasks,timeout,timeUnit)
<T>T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit)在指定的时间内取得第一个先执行完任务的结果值
```
public class MyCallableA implements Callable<String> {
    @Override
    public String call() throws Exception {
        System.out.println("MyCallableA begin " + System.currentTimeMillis());
        try {
            for(int i=0;i<2000000;i++){
                Math.random();
                Math.random();
                Math.random();
                System.out.println("MyCallA " + (i+1));
                if(Thread.currentThread().isInterrupted()==true){
                System.out.println(" is interrupted !");
                throw new NullPointerException();
            }
        }
        } catch (NullPointerException e) {
            e.printStackTrace();
        }
        System.out.println("MyCallableA end " + System.currentTimeMillis());
        return "returnA";
    }
}

public class RunTest {
    public static void main(String[] args) {
        ArrayList<Callable<String>> list = new ArrayList<>();
        list.add(new MyCallableA());
        ExecutorService executor = Executors.newCachedThreadPool();
        String getValueA = null;
        try {
            getValueA = executor.invokeAny(list, 1, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
        System.out.println("zzz " + getValueA);
        executor.shutdown();
    }
}
捕获到了所有异常
MyCallA 115296
 is interrupted !
MyCallableA end 1480325612457
zzz null
java.lang.NullPointerException
	at com.MyCallableA.call(MyCallableA.java:20)
	at com.MyCallableA.call(MyCallableA.java:8)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
java.util.concurrent.TimeoutException
	at java.util.concurrent.AbstractExecutorService.doInvokeAny(AbstractExecutorService.java:184)
	at java.util.concurrent.AbstractExecutorService.invokeAny(AbstractExecutorService.java:225)
	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147)
```

invokeAll(Collection tasks) 返回所有任务的执行结果，并且此方法执行的效果也是阻塞执行的。



















