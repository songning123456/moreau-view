#### 进程和线程的区别是什么？
| 进程 | 线程 | 
| :---- | :---- |
|<div style='width: 450px'>1. 进程是拥有资源的基本单位；<br>2. 进程拥有独立的地址空间；<br>3. 同一个进程的线程共享该进程的地址空间；<br>4. 进程上下文切换相对线程上下文切换会消耗更多的资源；<br>5. 一个进程必须至少拥有一个线程；<br>6. 进程的通信有多种方式，包括管道、共享内存、消息等等。</div>|1. 线程是CPU调度的基本单位；<br>2. 一个线程死掉就等于整个进程死掉，所以多进程的程序相对于多线程的程序来说会更健壮；<br>3. 线程通过进程内的资源进行通信。|


#### 创建线程有几种不同的方式？你喜欢哪一种？为什么？
1. 继承Thread类。
2. 实现Runnable接口。
3. 应用程序可以使用Executor框架来创建线程池。


实现Runnable接口这种方式更受欢迎，因为这不需要继承Thread类。在应用设计中已经继承了别的对象的情况下，这需要多继承(而Java不支持多继承)，只能实现接口。同时，线程池也是非常高效的，很容易实现和使用。


#### 概括的解释下线程的几种可用状态？
| 状态 | 解释 | 
| :---- | :---- |
| 就绪(Runnable) | 线程准备运行，不一定立马就能开始执行。 |
| 运行中(Running) | 进程正在执行线程的代码。 |
| 等待中(Waiting) | 线程处于阻塞的状态，等待外部的处理结束。 |
| 睡眠中(Sleeping) | 线程被强制睡眠。 |
| I/O阻塞(Blocked on I/O) | 等待I/O操作完成。 |
| 同步阻塞(Blocked on Synchronization) | 等待获取锁。 |
| 死亡(Dead) | 线程完成了执行。 |


#### 如何停止一个线程？
1. 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
2. 使用stop方法强行终止，但是不推荐这个方法，因为stop和suspend及resume一样都是过期作废的方法。
3. 使用interrupt方法中断线程。


#### sleep和wait的区别？
| sleep | wait | 
| :----- | :----- | 
| <div style='width: 400px'>1. Thread类中方法；<br>2. 可以在任何地方使用；<br>3. 程序暂停执行指定的时间，让出cpu该其他线程，但是它的监控状态依然保持者，当指定的时间到了又会自动恢复运行状态，在调用sleep()方法的过程中，线程不会释放对象锁。</div> | 1. Object类中的方法；<br>2. 只能在同步代码块或同步方法中使用；<br>3. 线程会放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象调用notify()方法后本线程才进入对象锁定池准备。 | 


#### 现在有T1、T2、T3三个线程，你怎样保证T2在T1执行完后执行，T3在T2执行完后执行？
**join()方法**
```java
// Thread1
public class Thread1 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 2; i++) {
            System.out.println(Thread.currentThread().getName() + "=====>" + i);
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// Thread2
public class Thread2 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 2; i++) {
            System.out.println(Thread.currentThread().getName() + "=====>" + i);
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// Thread3
public class Thread3 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 2; i++) {
            System.out.println(Thread.currentThread().getName() + "=====>" + i);
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// 测试方法
public class TestJoin1Main {
    public static void main(String[] args) {
        Thread thread1 = new Thread(new Thread1(), "线程1");
        Thread thread2 = new Thread(new Thread2(), "线程2");
        Thread thread3 = new Thread(new Thread3(), "线程3");
        try {
            thread1.start();
            thread1.join();
            thread2.start();
            thread2.join();
            thread3.start();
            thread3.join();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

// 结果
线程1=====>0
线程1=====>1
线程2=====>0
线程2=====>1
线程3=====>0
线程3=====>1
```
```java
// 测试方法
public class TestJoin2Main {
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 2; i++) {
                System.out.println(Thread.currentThread().getName() + "=====>" + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "线程1");
        Thread thread2 = new Thread(() -> {
            try {
                thread1.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            for (int i = 0; i < 2; i++) {
                System.out.println(Thread.currentThread().getName() + "=====>" + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "线程2");
        Thread thread3 = new Thread(() -> {
            try {
                thread2.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            for (int i = 0; i < 2; i++) {
                System.out.println(Thread.currentThread().getName() + "=====>" + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "线程3");
        thread1.start();
        thread2.start();
        thread3.start();
    }
}

// 结果
线程1=====>0
线程1=====>1
线程2=====>0
线程2=====>1
线程3=====>0
线程3=====>1
```


#### 多线程之间是如何进行信息交互的？
| 方法 | 解释 | 
| :----- | :----- | 
| <div style="width: 300px">void notify()</div> | 唤醒在此对象监视器上等待的单个线程。 | 
| <div style="width: 300px">void notifyAll()</div> | 唤醒在此对象监视器上等待的所有线程。 | 
| <div style="width: 300px">void wait()</div> | 导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法。 | 
| <div style="width: 300px">void wait(long timeout)</div> | 导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法，或者超过指定的时间量。 | 
| <div style="width: 300px">void wait(long timeout,int nanos)</div> | 导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法，或者其他某个线程中断当前线程，或者已超过某个实际时间量。 | 


#### 多线程的几种通讯方式？
**同步**


多个线程通过synchronized关键字这种方式来实现线程间的通信。
```java
public class MyObject {
    synchronized public void methodA() {
        //do something....
    }
    synchronized public void methodB() {
        //do some other thing
    }
}

public class ThreadA extends Thread {
    private MyObject object;
    @Override
    public void run() {
        super.run();
        object.methodA();
    }
}

public class ThreadB extends Thread {
    private MyObject object;
    @Override
    public void run() {
        super.run();
        object.methodB();
    }
}

public class Run {
    public static void main(String[] args) {
        MyObject object = new MyObject();
        //线程A与线程B 持有的是同一个对象:object
        ThreadA a = new ThreadA(object);
        ThreadB b = new ThreadB(object);
        a.start();
        b.start();
    }
}
```
由于线程A和线程B持有同一个MyObject类的对象object，尽管这两个线程需要调用不同的方法，但是它们是同步执行的，比如：线程B需要等待线程A执行完了methodA()方法之后，它才能执行methodB()方法。这样，线程A和线程B就实现了通信。这种方式，本质上就是“共享内存”式的通信。多个线程需要访问同一个共享变量，谁拿到了锁(获得了访问权限)，谁就可以执行。


**while轮询的方式**
```java
import java.util.ArrayList;
import java.util.List;
public class MyList {
    private List<String> list = new ArrayList<String>();
    public void add() {
        list.add("elements");
    }
    public int size() {
        return list.size();
    }
}
 
import mylist.MyList;
public class ThreadA extends Thread {
    private MyList list;
    public ThreadA(MyList list) {
        super();
        this.list = list;
    }
    @Override
    public void run() {
        try {
            for (int i = 0; i < 10; i++) {
            list.add();
            System.out.println("添加了" + (i + 1) + "个元素");
            Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
 
import mylist.MyList;
public class ThreadB extends Thread {
    private MyList list;
    public ThreadB(MyList list) {
        super();
        this.list = list;
    }
    @Override
    public void run() {
        try {
            while (true) {
                if (list.size() == 5) {
                    System.out.println("==5, 线程b准备退出了");
                    throw new InterruptedException();
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

import mylist.MyList;
import extthread.ThreadA;
import extthread.ThreadB;
public class Test {
    public static void main(String[] args) {
        MyList service = new MyList();
        ThreadA a = new ThreadA(service);
        a.setName("A");
        a.start();
        ThreadB b = new ThreadB(service);
        b.setName("B");
        b.start();
    }
}
```
在这种方式下，线程A不断地改变条件，线程ThreadB不停地通过while语句检测这个条件(list.size()==5)是否成立 ，从而实现了线程间的通信。但是这种方式会浪费CPU资源。之所以说它浪费资源，是因为JVM调度器将CPU交给线程B执行时，它没做啥`有用`的工作，只是在不断地测试某个条件是否成立。就类似于现实生活中，某个人一直看着手机屏幕是否有电话来了，而不是在干别的事情，当有电话来时，响铃通知TA电话来了。这种方式还存在另外一个问题：轮询的条件的可见性问题。线程都是先把变量读取到本地线程栈空间，然后再去再去修改的本地变量。因此，如果线程B每次都在取本地的条件变量，那么尽管另外一个线程已经改变了轮询的条件，它也察觉不到，这样也会造成死循环。


**wait/notify机制**
```java
import java.util.ArrayList;
import java.util.List;
public class MyList {
    private static List<String> list = new ArrayList<String>();
    public static void add() {
        list.add("anyString");
    }
    public static int size() {
        return list.size();
    }
}

public class ThreadA extends Thread {
    private Object lock;
    public ThreadA(Object lock) {
        super();
        this.lock = lock;
    }
    @Override
    public void run() {
        try {
            synchronized (lock) {
                if (MyList.size() != 5) {
                    System.out.println("wait begin " + System.currentTimeMillis());
                    lock.wait();
                    System.out.println("wait end " + System.currentTimeMillis());
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class ThreadB extends Thread {
    private Object lock;
    public ThreadB(Object lock) {
        super();
        this.lock = lock;
    }
    @Override
    public void run() {
        try {
            synchronized (lock) {
                for (int i = 0; i < 10; i++) {
                    MyList.add();
                    if (MyList.size() == 5) {
                        lock.notify();
                        System.out.println("已经发出了通知");
                    }
                    System.out.println("添加了" + (i + 1) + "个元素!");
                    Thread.sleep(1000);
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class Run {
    public static void main(String[] args) {
        try {
            Object lock = new Object();
            ThreadA a = new ThreadA(lock);
            a.start();
            Thread.sleep(50);
            ThreadB b = new ThreadB(lock);
            b.start();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
线程A要等待某个条件满足时(list.size() == 5)，才执行操作。线程B则向list中添加元素，改变list的size。A、B之间如何通信的呢？也就是说，线程A如何知道list.size()已经为5了呢？这里用到了Object类的wait()和notify()方法。当条件未满足时(list.size() != 5)，线程A调用wait()放弃CPU，并进入阻塞状态。不像while轮询那样占用CPU。当条件满足时，线程B调用notify()通知线程A，所谓通知线程A，就是唤醒线程A，并让它进入可运行状态。这种方式的一个好处就是CPU的利用率提高了。但是也有一些缺点：比如，线程B先执行，一下子添加了5个元素并调用了notify()发送了通知，而此时线程A还执行；当线程A执行并调用wait()时，那它永远就不可能被唤醒了。因为，线程B已经发了通知了，以后不再发通知了。这说明：通知过早，会打乱程序的执行逻辑。


**管道通信**


`java.io.PipedInputStream`和`java.io.PipedOutputStream`。


#### 说说线程池的创建方式？
**newCachedThreadPool**


创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
```java
// 无限大小线程池JVM自动回收
ExecutorService newCachedThreadPool = Executors.newCachedThreadPool();
for (int i = 0; i < 10; i++) {
    final int temp = i;
    newCachedThreadPool.execute(new Runnable() {
        @Override
        public void run() {
            try {
                Thread.sleep(100);
            } catch (Exception e) {
                // TODO: handle exception
            }
            System.out.println(Thread.currentThread().getName() + ",i:" + temp);
        }
    });
}
```
<span style="color: red">总结：线程池为无限大，当执行第二个任务时第一个任务已经完成，会复用执行第一个任务的线程，而不用每次新建线程。</span>


**newFixedThreadPool**


创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
```java
ExecutorService newFixedThreadPool = Executors.newFixedThreadPool(5);
for (int i = 0; i < 10; i++) {
    final int temp = i;
    newFixedThreadPool.execute(new Runnable() {
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getId() + ",i:" + temp);
        }
    });
}
```
<span style="color: red">总结：因为线程池大小为3，每个任务输出index后sleep 2秒，所以每两秒打印3个数字。定长线程池的大小最好根据系统资源进行设置，如Runtime.getRuntime().availableProcessors()。</span>


**newScheduledThreadPool**


创建一个定长线程池，支持定时及周期性任务执行。
```java
// 延时3秒执行
ScheduledExecutorService newScheduledThreadPool = Executors.newScheduledThreadPool(5);
		for (int i = 0; i < 10; i++) {
			final int temp = i;
			newScheduledThreadPool.schedule(new Runnable() {
				public void run() {
					System.out.println("i:" + temp);
				}
			}, 3, TimeUnit.SECONDS);
}
```


**newSingleThreadExecutor**


创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO、LIFO优先级)执行。
```java
ExecutorService newSingleThreadExecutor = Executors.newSingleThreadExecutor();
for (int i = 0; i < 10; i++) {
    final int index = i;
    newSingleThreadExecutor.execute(new Runnable() {
        @Override
        public void run() {
            System.out.println("index:" + index);
            try {
                Thread.sleep(200);
            } catch (Exception e) {
                // TODO: handle exception
            }
        }
    });
}
```
<span style="color: red">注意: 结果依次输出，相当于顺序执行各个任务。</span>


#### 线程池的底层工作原理？
![线程池](/images/Thread/ThreadPool.jpg)


1. 在创建了线程池后，等待提交过来的任务请求。
2. 在调用execute()方法添加一个请求任务时，线程池会做如下判断：
```
如果正在运行的线程数量小于corePoolSize=5，那么马上创建线程运行这个任务；
如果正在运行的线程数量大于或等于corePoolSize=5，那么将这个任务放入队列；
如果这时候队列满了且正在运行的线程数量还小于maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务；
如果队列满了且正在运行的线程数量大于或等于maximumPoolSize,那么线程池会启动饱和拒绝策略来执行。
```
3. 当一个线程完成任务时，它会从队列中去下一个任务来执行。
4. 当一个线程无事可做超过一定的时间(keepAliveTime)时，线程池会判断：如果当前运行的线程数大于corePoolSize=5，那么这个线程就被停掉。所以线程池的所有任务完成后它最终会收缩到corePoolSize=5的大小。


#### 能说说线程池的核心配置参数？ThreadPoolExecutor有哪些拒绝策略？
| 参数 | 默认值 | 解释 | 
| :----- | :----- | :----- | 
| corePoolSize(核心线程数) | 1 | 1.核心线程会一直存在，即使没有任务执行； <br> 2.当线程数小于核心线程数的时候，即使有空闲线程，也会一直创建线程直到达到核心线程数； <br> 3.设置allowCoreThreadTimeout=true(默认false)时，核心线程会超时关闭。 | 
| queueCapacity(任务队列容量) | Integer.MAX_VALUE | 也叫阻塞队列，当核心线程都在运行，此时再有任务进来，会进入任务队列，排队等待线程执行。 | 
| maxPoolSize(最大线程数) | Integer.MAX_VALUE | 1.线程池里允许存在的最大线程数量； <br> 2.当任务队列已满，且线程数量大于等于核心线程数时，会创建新的线程执行任务； <br> 3.线程池里允许存在的最大线程数量。当任务队列已满，且线程数量大于等于核心线程数时，会创建新的线程执行任务。 | 
| keepAliveTime(线程空闲时间) | 60秒 | 1.当线程空闲时间达到keepAliveTime时，线程会退出(关闭)，直到线程数等于核心线程数； <br> 2.如果设置了allowCoreThreadTimeout=true，则线程会退出直到线程数等于零。 | 
| allowCoreThreadTimeout(允许核心线程超时) | false | ———— | 
| rejectedExecutionHandler(任务拒绝处理器) | AbortPolicy() | 1.当线程数量达到最大线程数，且任务队列已满时，会拒绝任务； <br> 2.调用线程池shutdown()方法后，会等待执行完线程池的任务之后，再shutdown()。如果在调用了shutdown()方法和线程池真正shutdown()之间提交任务，会拒绝新任务。 | 


👉 [四种线程池拒绝策略](https://blog.csdn.net/suifeng629/article/details/98884972)


| 拒绝策略 | 解释 | 
| :----- | :----- | 
| AbortPolicy | 直接抛异常。 | 
| DiscardPolicy | 抛弃当前将要加入队列的任务。 | 
| DiscardOldestPolicy | 抛弃任务队列中最旧任务。 | 
| CallerRunsPolicy | 当前任务会强制调用run先执行，任务将由调用者线程(可能是主线程)去执行。缺点可能会阻塞主线程。 | 
| 自定义 | 如果后续慢慢的队列里没任务了，线程空闲了，超过corePoolSize的线程会自动释放掉，在keepAliveTime之后就会释放。 | 


#### JDK提供了多少种阻塞队列？如果在线程池中使用无界阻塞队列会发生什么问题？
| 阻塞队列 | 解释 | 
| :----- | :----- | 
| ArrayBlockingQueue | 一个由数组结构组成的有界阻塞队列。 | 
| LinkedBlockingQueue | 一个由链表结构组成的有界阻塞队列。 | 
| PriorityBlockingQueue | 一个支持优先级排序的无界阻塞队列。 | 
| DelayQueue | 一个使用优先级队列实现的无界阻塞队列。 | 
| SynchronousQueue | 一个不存储元素的阻塞队列。 | 
| LinkedTransferQueue | 一个由链表结构组成的无界阻塞队列。 | 
| LinkedBlockingQueue | 一个由链表结构组成的双向阻塞队列。 | 


| 线程池 | 默认队列 | 解释 |
| :----- | :----- | 
| Executors.newFixedThreadPool(10) | LinkedBlockingQueue | 无限加入队列。 |
| Executors.newScheduledThreadPool(10) | DelayedWorkQueue | 队列如果满了，阻塞。 |
| Executors.newSingleThreadScheduledExecutor() | DelayedWorkQueue | 队列如果满了，阻塞。 |
| Executors.newCachedThreadPool() | SynchronousQueue | 队列如果满了，抛异常。 |
| Executors.newSingleThreadExecutor() | LinkedBlockingQueue | 无限加入队列。 |


因为调用异常，会调用超时，线程处理任务时间是超时时间，线程池等待队列，会变得越来越大，此时会导致内存飙升起来，而且还可能导致OOM，内存溢出或者频繁的GC。


#### 如果线上机器突然宕机，线程池的阻塞队列中的请求怎么办？该如何解决？
机器宕机，必然会导致线程池里的积压的任务丢失。


在提交一个任务到线程池里去，提交之前，将这个任务信息持久化到数据库里，此时的状态为`未提交`，提交成功之后，更新任务信息的状态为`提交成功`。当任务完成的时候，更新任务信息的状态为`已完成`；当宕机的机器重启的时候，可以开启一个后台线程，扫描数据库里`未提交`和`已提交`的任务，可以把任务读取出来，重新提交到线程池中，继续进行执行，被调用的方法一定做好幂等操作，防止请求重复执行。


#### 如何合理的配置线程池？
<table>
    <tr>
        <th>角度</th>
        <th>类型</th>
        <th>解释</th>
    </tr>
    <tr>
        <td rowspan="3">任务的性质</td>
        <td style="width: 150px">CPU密集型任务</td>
        <td>配置尽可能少的线程数量，如配置<span style="color:red">Ncpu+1</span>个线程的线程池。</td>
    </tr>
    <tr>
        <td style="width: 150px">IO密集型任务</td>
        <td>由于需要等待IO操作，线程并不是一直在执行任务，则配置尽可能多的线程，如<span style="color:red">2*Ncpu</span>。</td>
    </tr>
    <tr>
        <td style="width: 150px">混合型任务</td>
        <td>如果可以拆分，则将其拆分成一个CPU密集型任务和一个IO密集型任务，只要这两个任务执行的时间相差不是太大，那么分解后执行的吞吐率要高于串行执行的吞吐率，如果这两个任务执行时间相差太大，则没必要进行分解。我们可以通过Runtime.getRuntime().availableProcessors()方法获得当前设备的CPU个数。</td>
    </tr>
    <tr>
      <td>任务的优先级</td>
      <td style="width: 150px">高、中和低</td>
      <td>使用优先级队列PriorityBlockingQueue来处理。它可以让优先级高的任务先得到执行，需要注意的是如果一直有优先级高的任务提交到队列里，那么优先级低的任务可能永远不能执行。</td>
    </tr>
    <tr>
        <td>任务的执行时间</td>
        <td style="width: 150px">长、中和短</td>
        <td>交给不同规模的线程池来处理，或者也可以使用优先级队列，让执行时间短的任务先执行。</td>
    </tr>
    <tr>
        <td>任务的依赖性</td>
        <td style="width: 150px">是否依赖其他系统资源，如数据库连接</td>
        <td>依赖数据库连接池的任务，因为线程提交SQL后需要等待数据库返回结果，如果等待的时间越长CPU空闲时间就越长，那么线程数应该设置越大，这样才能更好的利用CPU。</td>
    </tr>
</table>


#### 线程池中当coreSize=0会发生什么？
👉 [自定义线程池核心线程数设置0任务还会执行吗？](https://blog.csdn.net/u010112098/article/details/115139590)


👉 [最近踩的一个线程池的坑: coreSize=0&&queueCapacity>1](https://www.chenjianjx.com/最近踩的一个线程池的坑：-coresize-0-queuecapacity-1/)


#### ThreadLocal为什么被设计为弱引用？
👉 [谈谈ThreadLocal为什么被设计为弱引用](https://zhuanlan.zhihu.com/p/304240519)


👉 [ThreadLocal的原理、作用、使用弱引用原因、应用举例](https://www.cnblogs.com/shen-qian/p/12108655.html)


#### ThreadLocalMap中的Entry是什么数据结构，数组还是链表？
👉 [为什么ThreadLocal类内部的ThreadLocalMap要用Entry数组实现？](https://www.zhihu.com/question/279007680)


#### 锁的升级？什么是轻量级锁？什么是重量级锁？
👉 [多线程锁的升级原理是什么?](https://blog.csdn.net/meism5/article/details/90321826)


👉 [synchronized 锁的升级原理是什么?](https://www.cnblogs.com/ConstXiong/p/11687975.html)