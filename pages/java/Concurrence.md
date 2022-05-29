#### 什么是死锁(deadlock)？如何才能产生死锁？
当一个线程永远地持有一个锁，并且其他线程都尝试获得这个锁时，那么它们将永远被阻塞。在线程A持有锁L并想获得锁M的同时，线程B持有锁M并尝试获得锁L，那么这两个线程将永远地等待下去。这种就是最简答的死锁形式(或者叫做“抱死”)。


| 条件 | 解释 | 
| :----- | :----- | 
| 互斥条件 | 所谓互斥就是进程在某一时间内独占资源。 | 
| 请求与保持条件 | 一个进程因请求资源而阻塞时，对已获得的资源保持不放。 | 
| 不剥夺条件 | 进程已获得资源，在末使用完之前，不能强行剥夺。 | 
| 循环等待条件 | 若干进程之间形成一种头尾相接的循环等待资源关系。 | 


#### 如何预防死锁？死锁的解决办法？
| 条件 | 解释 | 
| :----- | :----- | 
| 打破互斥条件 | <div style="width: 550px">即允许进程同时访问某些资源。但是，有的资源是不允许被同时访问的，像打印机等等，这是由资源本身的属性所决定的。所以，这种办法并无实用价值。</div> | 
| 打破不可抢占条件 | <div style="width: 550px">即允许进程强行从占有者那里夺取某些资源。就是说，当一个进程已占有了某些资源，它又申请新的资源，但不能立即被满足时，它必须释放所占有的全部资源，以后再重新申请。它所释放的资源可以分配给其它进程。这就相当于该进程占有的资源被隐蔽地强占了。这种预防死锁的方法实现起来困难，会降低系统性能。</div> | 
| 打破占有且申请条件 | <div style="width: 550px">可以实行资源预先分配策略。即进程在运行前一次性地向系统申请它所需要的全部资源。如果某个进程所需的全部资源得不到满足，则不分配任何资源，此进程暂不运行。只有当系统能够满足当前进程的全部资源需求时，才一次性地将所申请的资源全部分配给该进程。由于运行的进程已占有了它所需的全部资源，所以不会发生占有资源又申请资源的现象，因此不会发生死锁。</div> | 
| 打破循环等待条件 | <div style="width: 550px">实行资源有序分配策略。采用这种策略，即把资源事先分类编号，按号分配，使进程在申请，占用资源时不会形成环路。所有进程对资源的请求必须严格按资源序号递增的顺序提出。进程占用了小号资源，才能申请大号资源，就不会产生环路，从而预防了死锁。</div> | 


在Account中包含一个唯一的，不可变的值。比如说账号等。通过对这个值对对象进行排序(snowFlakeId)。
```java
@Data
public class Account {
    private Integer id;
    private Integer balance;
    public Account(Integer id, Integer balance) {
        this.id = id;
        this.balance = balance;
    }
    /**
     * 借记
     *
     * @param money
     * @throws Exception
     */
    public void debit(int money) throws Exception {
        Thread.sleep(500);
        balance = balance + money;
    }
    /**
     * 信贷
     *
     * @param money
     * @throws Exception
     */
    public void credit(int money) throws Exception {
        Thread.sleep(500);
        balance = balance - money;
    }
    public int compareTo(int money) {
        if (balance > money) {
            return 1;
        } else if (balance < money) {
            return -1;
        } else {
            return 0;
        }
    }
}
```
```java
public class Helper {
    public void transfer(Account fromAccount, Account toAccount, int amount) throws Exception {
        if (fromAccount.compareTo(amount) < 0) {
            throw new Exception("金钱不够");
        } else {
            fromAccount.credit(amount);
            toAccount.debit(amount);
        }
    }
}
```
```java
public class TransferThread extends Thread {
    public static List<Account> accountList = Arrays.asList(new Account(1, 1000), new Account(2, 1000));
    @Override
    public void run() {
        int i = new Random().nextInt(2);
        Account fromAccount, toAccount;
        if (i == 0) {
            fromAccount = accountList.get(1);
            toAccount = accountList.get(0);
        } else {
            fromAccount = accountList.get(0);
            toAccount = accountList.get(1);
        }
        try {
            Lock.transferMoney(fromAccount, toAccount, 1);
        } catch (Exception e) {
            System.out.println("发生异常-------" + e);
        }
    }
}
```
```java
public class Lock {
    public static void transferMoney(Account fromAccount, Account toAccount, int amount) throws Exception {
        int fromId = fromAccount.getId();
        int toId = toAccount.getId();
        System.out.println("账户" + fromId + " 向账户 " + toId + " 转账");
        if (fromId < toId) {
            synchronized (fromAccount) {
                synchronized (toAccount) {
                    new Helper().transfer(fromAccount, toAccount, amount);
                }
            }
        } else if (fromId > toId) {
            synchronized (toAccount) {
                synchronized (fromAccount) {
                    new Helper().transfer(fromAccount, toAccount, amount);
                }
            }
        } else {
            throw new Exception("from,to不能相等!!!");
        }
    }
}
```
```java
public class DemoMain {
    public static void main(String[] args) {
        for (int i = 0; i < 50; i++) {
            new TransferThread().start();
        }
    }
}
```


#### 说说synchronized关键字的底层原理？
👉 [synchronized 关键字底层原理](https://www.jianshu.com/p/570410236ff5)


synchronized同步语句块的实现，使用的是`monitorenter`和`monitorexit`指令，其中monitorenter指令指向同步代码块的开始位置，monitorexit指令则指明同步代码块的结束位置。 当执行monitorenter指令时，线程试图获取锁，也就是获取monitor(monitor对象存在于每个Java对象的对象头中，synchronized锁便是通过这种方式获取锁的，这也是为什么Java中任意对象都可以作为锁的原因)的持有权。当计数器为0，则可以成功获取，获取后将锁计数器设为1，也就是加1；相应的，在执行monitorexit指令后，将锁计数器设为0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止。


#### 你知道synchronized的使用场景吗，它是如何使用的？
| 分类 | 被锁的对象 | 伪代码 | 解释 | 
| :----- | :----- | :----- | :----- | 
| <div style="width: 100px">实例方法</div> | <div style="width: 160px">类的实例对象</div> | public synchronized void method() { ... } | 实例方法，锁住的是该类的实例对象。 |
| <div style="width: 100px">静态方法</div> | <div style="width: 160px">类对象</div> | public static synchronized void method() { ... } | 静态方法，锁住的是类对象。 |
| <div style="width: 100px">实例对象</div> | <div style="width: 160px">类的实例对象</div> | synchronized(this) { ... } | 同步代码块，锁住的是该类的实例对象。 |
| <div style="width: 100px">Class对象</div> | <div style="width: 160px">类对象</div> | synchronized(Clazz.class) { ... } | 同步代码块，锁住的是该类的类对象。 |
| <div style="width: 100px">任意实例对象</div> | <div style="width: 160px">实例对象Object</div> | Object object = new Object(); synchronized(object) { ... } | 同步代码块，锁住的是配置的实例对象。 |


#### synchronized是可重入锁吗？
当线程请求一个由其它线程持有的对象锁时，该线程会阻塞，而当线程请求由自己持有的对象锁时，如果该锁是重入锁，请求就会成功，否则阻塞。
```java
public class Xttblog extends SuperXttblog {

    public static void main(String[] args) {
        Xttblog child = new Xttblog();
        child.doSomething();
    }

    public synchronized void doSomething() {
        System.out.println("child.doSomething()" + Thread.currentThread().getName());
        doAnotherThing(); // 调用自己类中其他的synchronized方法
    }

    private synchronized void doAnotherThing() {
        super.doSomething(); // 调用父类的synchronized方法
        System.out.println("child.doAnotherThing()" + Thread.currentThread().getName());
    }

}
 
class SuperXttblog {
    public synchronized void doSomething() {
        System.out.println("father.doSomething()" + Thread.currentThread().getName());
    }
}
```
```java
child.doSomething()Thread-5492
father.doSomething()Thread-5492
child.doAnotherThing()Thread-5492
```


验证出synchronized是可重入锁了。因为这些方法输出了相同的线程名称，表明即使递归使用synchronized也没有发生死锁，证明其是可重入的。


重入锁实现可重入性原理或机制是每一个锁关联一个线程持有者和计数器，当计数器为0时表示该锁没有被任何线程持有，那么任何线程都可能获得该锁而调用相应的方法；当某一线程请求成功后，JVM会记下锁的持有线程，并且将计数器置为1；此时其它线程请求该锁，则必须等待；而该持有锁的线程如果再次请求这个锁，就可以再次拿到这个锁，同时计数器会递增；当线程退出同步代码块时，计数器会递减，如果计数器为0，则释放该锁。


#### synchronized和Lock的区别？
| synchronized | Lock | 
| :----- | :----- | 
| 1. 关键字，内置语言实现；<br>2. 在线程发生异常时会自动释放锁，因此不会发生异常死锁；<br>3. 非中断锁，必须等待线程执行完成释放锁。 | 1. Lock是接口；<br>2. 异常时不会自动释放锁，所以需要在finally中实现释放锁；<br>3. 中断锁；<br>4. 使用读锁提高多线程读效率。 | 


#### 同步关键字(synchronized)修饰静态方法和修饰非静态方法的区别？
**修饰非静态方法**


```java
class Demo {
    private int i;
    Demo(int i) {
        this.i = i;
    }
    public synchronized void getI(String s) {
        for (int j = 0; j < 10000; j++) {
            System.out.println(i + "---------" + s);
        }
    }
}

public class ObjectLock implements Runnable {
    Demo demo;
    public ObjectLock(Demo demo) {
        this.demo = demo;
    }
    @Override
    public void run() {
        demo.getI(Thread.currentThread().getName());
    }
    public static void main(String[] args) {
        Demo demo = new Demo(1);
        Thread thread = new Thread(new ObjectLock(demo), "thread0");
        Thread thread1 = new Thread(new ObjectLock(demo), "thread1");
        Thread thread2 = new Thread(new ObjectLock(new Demo(2)), "thread2");
        thread.start();
        thread1.start();
        thread2.start();
    }
}
```
```
// 非静态方法结果
...
1---------thread0
1---------thread0
2---------thread2
1---------thread0
2---------thread2
1---------thread0
1---------thread1
1---------thread1
2---------thread2
1---------thread1
1---------thread1
...
```
<span style="color: red">可以看到Thread0和Thread2交替出现，Thread1和Thread2交替出现，但Thread0和Thread1不会交替出现。因为对非静态方法加锁，实际上是对调用该方法的对象加锁。Thread0和Thread1用的是同一个对象，所以互斥，但是Thread2则不受影响。</span>


**修饰静态方法**


```java
class Demo {
    static int i;
    Demo(int i) {
        Demo.i = i;
    }
    public static synchronized void staticGetI(String s) {
        for (int j = 0; j < 10000; j++) {
            System.out.println(i + "---------" + s);
        }
    }
}

// ObjectLock重复部分略
...
    @Override
    public void run() {
        Demo.staticGetI(Thread.currentThread().getName());
    }
...
```
```
// 静态方法结果
...
2---------thread0
2---------thread0
2---------thread2
2---------thread2
2---------thread1
2---------thread1
...
```
<span style="color: red">三个线程均互斥。当synchronized修饰一个static方法时，多线程下，获取的是类锁(即Class本身，注意：不是实例)，作用范围是整个静态方法，作用的对象是这个类的所有对象。</span>


**一个对象在两个线程中分别调用一个静态同步方法和一个非静态同步方法**


```java
public void run() {
    if (Thread.currentThread().getName().equals("thread0")){
        demo.staticGetI(Thread.currentThread().getName());
    }else if (Thread.currentThread().getName().equals("thread1")){
        demo.getI(Thread.currentThread().getName());
    }
} 
```
<span style="color: red">不会产生互斥。因为虽然是一个对象调用，但是两个方法的锁类型不同，调用的静态方法实际上是类对象在调用，即这两个方法产生的并不是同一个对象锁，因此不会互斥，会并发执行。</span>


#### 悲观锁和乐观锁的区别？
| 悲观锁 | 乐观锁 |
| :----- | :----- | 
|总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁(共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程)。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁、表锁等，读锁、写锁等，都是在做操作之前先上锁。Java中synchronized和ReentrantLock等独占锁就是悲观锁思想的实现。|总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库提供的类似于write_condition机制，其实都是提供的乐观锁。在Java中java.util.concurrent.atomic包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的。|


#### 能聊聊你对CAS的理解以及它的底层原理吗？
**<span style="color: red">内存值V，预期值A，要更新的新值B</span>**


![CAS](/images/Concurrence/CAS.png)


t1和t2线程都同时去访问同一变量V=56，所以他们会把主内存的值V完全拷贝一份到自己的工作内存空间，所以t1和t2线程的预期值都为V=56。假设t1在与t2线程竞争中线程t1能去更新变量的值，而其他线程都失败(失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次发起尝试)。t1线程去更新变量值改为B=57，然后写到内存中。此时对于t2来说，内存值变为了57，与预期值56不一致，就操作失败了(想改的值不再是原来的值)。


就是指当两者进行比较时，如果相等，则证明共享数据没有被修改，替换成新值，然后继续往下运行；如果不相等，说明共享数据已经被修改，放弃已经所做的操作，然后重新执行刚才的操作。容易看出CAS操作是基于共享数据不会被修改的假设，采用了类似于数据库的commit-retry的模式。当同步冲突出现的机会很少时，这种假设能带来较大的性能提升。


#### 你对JDK的AQS理解吗？AQS的实现原理是什么？
![AQS](/images/Concurrence/AQS.jpg)


👉 [深入分析AQS实现原理](https://segmentfault.com/a/1190000017372067)


#### 谈谈你对Java内存模型的理解？
![JMM](/images/Concurrence/JMM.png)


为了解决缓存一致性和cpu指令重排序的问题，同时屏蔽不同机器下CPU架构不一致的问题，于是java就定义了一种协议，这个协议就是Java内存模型(JMM)。


![JMM8](/images/Concurrence/JMM8.png)


| 八大原子操作 | 解释  |
| :----- | :----- | 
|lock(锁定)|作用于主内存的变量，它把一个变量标识为一条线程独占的状态。|
|read(读取)|作用于主内存的变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用。|
|load(载入)|作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中。|
|use(使用)|作用于工作内存的变量，它把工作内存中一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用变量的值的字节码指令时将会执行这个操作。|
|assign(赋值)|作用于工作内存的变量，它把一个从执行引擎接收的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。|
|store(存储)|作用于工作内存的变量，它把工作内存中一个变量的值传送到主内存中，以便随后的write操作使用。|
|write(写入)|作用于主内存的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中。如果要把一个变量从主内存拷贝到工作内存，那就要按顺序执行read和load操作，如果要把变量从工作内存同步回主内存，就要按顺序执行store和write操作。注意，Java内存模型只要求上述两个操作必须按顺序执行，但不要求是连续执行。也就是说read与load之间、store与write之间是可插入其他指令的，如对主内存中的变量a、b进行访问时，一种可能出现的顺序是read a、read b、load b、load a。|
|unlock(解锁)|作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。|


同时Java内存模型还规定了在执行上述八种原子操作时必须满足如下规则: 


1. 不允许read和load、store和write操作之一单独出现，即不允许一个变量从主内存读取了但工作内存不接受，或者工作内存发起回写了但主内存不接受的情况出现。
2. 不允许一个线程丢弃它最近的assign操作，即变量在工作内存中改变了之后必须把该变化同步回主内存。
3. 不允许一个线程无原因地(没有发生过任何assign操作)把数据从线程的工作内存同步回主内存中。
4. 一个新的变量只能在主内存中“诞生”，不允许在工作内存中直接使用一个未被初始化(load或assign)的变量，换句话说就是对一个变量实施use、store操作之前，必须先执行assign和load操作。
5. 一个变量在同一个时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。
6. 如果对一个变量执行lock操作，那将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或assign操作以初始化变量的值。
7. 如果一个变量事先没有被lock操作锁定，那就不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定的变量。
8. 对一个变量执行unlock操作之前，必须先把此变量同步回主内存中(执行store、write操作)。


由Java内存模型来直接保证的原子性变量操作包括read、load、assign、use、store和write这六个，我们大致可以认为，基本数据类型的访问、读写都是具备原子性的(例外就是long和double的非原子性协定)。


👉 [深入理解Java内存模型](https://www.jianshu.com/p/15106e9c4bf3)


👉 [一文看懂Java内存模型（JMM）](https://blog.csdn.net/c15158032319/article/details/117361782)



