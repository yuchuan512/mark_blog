title: Semaphore CountDownLatch CyclicBarrier
date: 2016-11-25 11:54:28
categories:
tags:
---
### Semaphore 可以控制线程的数量
semaphore.acquire()
semaphore.release()
semaphore.availablePermits()  可用许可证数量
semaphore.acquireUniterruptibly()  获取许可证过程中不允许被中断
semaphore.availablePermits()  可用许可证数量
semaphore.drainPermits()  返回立即可用的许可证数量，并且将许可证数量置0
semaphore.getQueueLength()   等待许可证的线程数量
semaphore.hasQueuedThreads() 是否有线程等待许可证
公平信号量运行的效果是线程启动的顺序与调用semaphore.acquire()的顺序有关，先启动的线程优先获得许可

semaphore.tryAcquire(long timeout,TimeUnit unit)
多进路-多处理-多出路
多进路-单处理-多出路 使用ReentrantLock 加锁单处理部分

Condition可以替代传统的线程间通信，**用await()替换wait()，用signal()替换notify()，用signalAll()替换notifyAll()。**
使用Semaphore完成生产者消费者
```
import java.util.concurrent.Semaphore;
public class Main {

    static class SharedData{
        int data = 0;0
        Semaphore semproducer = new Semaphore(1);
        Semaphore semconsume  = new Semaphore(0);

        public int consume() {
            try {
                semconsume.acquire();
                System.out.format("Consumed %d%n", data);
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                semproducer.release();
            }
            System.out.println("availableSemconsume " + semconsume.availablePermits());
            return data;

        }

        public void produce(int data){
            try {
                semproducer.acquire();
                this.data = data;
                System.out.format("produce %d%n", data);
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                semconsume.release();
            }
            System.out.println("availableproonsume " + semproducer.availablePermits());

        }
    }

    static class ConsumeThread implements Runnable{
        private SharedData sharedData;

        public ConsumeThread(SharedData sharedData){
            this.sharedData = sharedData;
        }

        @Override
        public void run() {
            for(int i=0;i<30;i++){
                try{
                    int data= sharedData.consume();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }
    }

    static class ProduceThread implements Runnable{
        private SharedData sharedData;

        public ProduceThread(SharedData sharedData){
            this.sharedData = sharedData;
        }

        @Override
        public void run() {
                for(int i=0;i<30;i++){
                    try{
                        sharedData.produce(i);
                    }catch (Exception e){
                        e.printStackTrace();
                    }
                }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        SharedData sharedData = new SharedData();
        Thread producer = new Thread(new ProduceThread(sharedData),"producer");
        Thread consumer = new Thread(new ConsumeThread(sharedData),"consumer");
        consumer.start();
        producer.start();

    }
}
```

### CountDownLatch
判断count计数不为0时，则当前线程呈wait状态，在屏障处等待。实际等待与继续运行的效果分别需要使用await()和countdown()方法来进行。调用countDown()方法将计数减1，当计数减到0时，等待的线程继续运行。而方法getCount()就是获得当前的计数个数。
计数无法被重置，如果需要重置计数，可以使用CyclicBarrier类
多个线程与同步点间阻塞的特性，线程必须都到达同步点。
```
public class CountThread extends Thread {
    private CountDownLatch maxRunner;

    public CountThread(CountDownLatch maxRunner) {
        this.maxRunner = maxRunner;
    }

    @Override
    public void run() {
        try {
            Thread.sleep(2000);
            maxRunner.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class CountDownTest {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(10);
        CountThread[] ct = new CountThread[(int) countDownLatch.getCount()];
        for(int i=0;i<ct.length;i++){
            ct[i] = new CountThread(countDownLatch);
            ct[i].setName("thread--  " + (i+1));
            ct[i].start();
        }
        countDownLatch.await();
        System.out.println("all come !");
    }
}

```
await(long timeout, TimeUnit unit)用法
```
public class MyService {
    private CountDownLatch countDownLatch = new CountDownLatch(1);

    public void  testMethod(){
        try{
            System.out.println("prepare");
            countDownLatch.await(3, TimeUnit.SECONDS);
            System.out.println("end");
        } catch (InterruptedException e) {
            e.printStackTrace();

        }
    }
}

public class CountDownTest {
    public static void main(String[] args) throws InterruptedException {
        MyService myService = new MyService();
        MyThread[] myThreads = new MyThread[3];
        for(int i=0;i<myThreads.length;i++){
            myThreads[i] = new MyThread(myService);
        }
        for(int i=0;i<myThreads.length;i++){
            myThreads[i].start();
        }
    }
}
```
使用CountDownLatch完成比赛流程
```
public class MyThread extends Thread {

    private CountDownLatch comingTag;
    private CountDownLatch waitTag;
    private CountDownLatch waitRunTag;
    private CountDownLatch beginTag;
    private CountDownLatch endTag;

    public MyThread(CountDownLatch comingTag, CountDownLatch waitTag, CountDownLatch waitRunTag,
                        CountDownLatch beginTag, CountDownLatch endTag){
        this.comingTag = comingTag;
        this.waitTag = waitTag;
        this.waitRunTag = waitRunTag;
        this.beginTag = beginTag;
        this.endTag = endTag;
    }

    @Override
    public void run() {
        System.out.println("运动员使用不同交通工具前往起跑点");
            try {
                Thread.sleep((int)(Math.random()*10));
                comingTag.countDown();
                System.out.println("等待裁判说准备");
                waitTag.await();
                System.out.println("各就各位！准备起跑");
                Thread.sleep((int)Math.random()*10);
                waitRunTag.countDown();
                beginTag.await();
                System.out.println("跑赛");
                endTag.countDown();
                System.out.println("运动员到达终点");
            } catch (InterruptedException e) {
                e.printStackTrace();
        }

    }
}

public class CountDownTest {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch comingTag = new CountDownLatch(10);
        CountDownLatch waitTag = new CountDownLatch(1);
        CountDownLatch waitRunTag = new CountDownLatch(10);
        CountDownLatch beginTag = new CountDownLatch(1);
        CountDownLatch endTag = new CountDownLatch(10);

        MyThread[] myThreads = new MyThread[10];
        for(int i=0;i<myThreads.length;i++){
            myThreads[i] = new MyThread(comingTag, waitTag, waitRunTag, beginTag, endTag);
            myThreads[i].start();
        }
        System.out.println("裁判等待运动员");
        comingTag.await();
        System.out.println("运动员到场，巡视5秒");
        Thread.sleep(5000);
        waitTag.countDown();
        waitRunTag.await();
        System.out.println("发令枪响起");
        beginTag.countDown();
        endTag.await();
        System.out.println("比赛结束");
    }
}

```

### CyclicBarrier ['saiklik]

1) CountDownLatch 一个线程或者多个线程，等待另外一个线程或多个线程完成某个事情之后才能继续执行。
2) CyclicBarrier 多个线程之间相互等待，任何一个线程完成之前，所有的线程都必须相互等待。
```
public class MyThread extends Thread{
    private CyclicBarrier cbRef;
    public MyThread(CyclicBarrier cbRef){
        this.cbRef = cbRef;
    }

    @Override
    public void run() {
        try {
            Thread.sleep((int)Math.random()*10);
            System.out.println(Thread.currentThread().getName()+" 到了");
            cbRef.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (BrokenBarrierException e) {
            e.printStackTrace();
        }

    }
}

public class Test {
    public static void main(String[] args) {
        CyclicBarrier cbRef = new CyclicBarrier(5, new Runnable() {
            @Override
            public void run() {
                System.out.println("all come !");
            }
        });
        MyThread[] myThreads = new MyThread[5];
        for(int i=0;i<myThreads.length;i++){
            myThreads[i] = new MyThread(cbRef);
            myThreads[i].start();
        }
    }
}

```
 * CyclicBarrier也支持分批进行比赛的效果。

方法getNumberWaiting和getParties
```
public class MyService {
    CyclicBarrier cyclicBarrier = new CyclicBarrier(3, new Runnable() {
        @Override
        public void run() {
            System.out.println("结束了");
        }
    });

    public void testMethod(){
        System.out.println(Thread.currentThread().getName() +" 准备");
        if(Thread.currentThread().getName().equalsIgnoreCase("C")){
            try {
                Thread.sleep(Integer.MAX_VALUE);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        try {
            cyclicBarrier.await();
            System.out.println(Thread.currentThread().getName()+" 开始");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (BrokenBarrierException e) {
            e.printStackTrace();
        }
    }
}

public class Test {
    public static void main(String[] args) throws InterruptedException {
        MyService myService = new MyService();
        MyThread myThreadA = new MyThread(myService);
        MyThread myTHreadB = new MyThread(myService);
        MyThread myTHreadC = new MyThread(myService);
        myTHreadC.setName("C");
        myThreadA.start();
        myTHreadB.start();
        myTHreadC.start();
        Thread.sleep(2000);
        System.out.println("parties is " + myService.cyclicBarrier.getParties());
        System.out.println("thread is waiting " + myService.cyclicBarrier.getNumberWaiting());

    }
}


```
结果
```
Thread-0 准备
C 准备
Thread-1 准备
parties is 3
thread is waiting 2
```

验证屏障重置
```

public class MyThread extends Thread{
    private CyclicBarrier cbRef;
    public MyThread(CyclicBarrier cbRef){
        this.cbRef = cbRef;
    }

    @Override
    public void run() {
        try {
            cbRef.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (BrokenBarrierException e) {
            e.printStackTrace();
        }

    }
}

public class Test {
    public static void main(String[] args) throws InterruptedException {
        CyclicBarrier cbRef = new CyclicBarrier(3);
        MyThread thread1 = new MyThread(cbRef);
        thread1.start();
        Thread.sleep(1000);
        System.out.println(cbRef.getNumberWaiting());

        MyThread thread2 = new MyThread(cbRef);
        thread2.start();
        Thread.sleep(1000);
        System.out.println(cbRef.getNumberWaiting());

        MyThread thread3 = new MyThread(cbRef);
        thread3.start();
        Thread.sleep(1000);
        System.out.println(cbRef.getNumberWaiting());

        MyThread thread4 = new MyThread(cbRef);
        thread4.start();
        Thread.sleep(1000);
        System.out.println(cbRef.getNumberWaiting());
    }
}

结果
1
2
0
1
```





