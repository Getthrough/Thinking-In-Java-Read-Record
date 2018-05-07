# Thinking-In-Java-Read-Record
## Java 编程思想读书笔记
### Chapter 2 一切都是对象

* 方法名和参数列表被称为“方法签名”，他们可以唯一地标识一个方法。唯一性与方法的返回值无关。因为当调用方法不考虑方法的返回值时（只是为了调用方法而产生的效果），两个不同返回值的方法在代码层面是完全相同的。
    ```
    # 方法定义
    void methodA() {// 1
      // do something
    }

    int methodA() {// 2
      // dosomething diff
    }
    # 方法调用时，代码完全相同
      object.methodA();// invoke "1"
      object.methodA();// invoke "2"
    ```
* 为了避免类名称重复，使用小写、倒叙的域名作为包名前缀。
* `java.lang` 是默认导入每个`java`文件中的，它的所有类可以被直接使用。
* `main()`方法中的`args`参数用于存储命令行参数。

### Chapter 5 初始化与清理
* 在使用`new`关键字创建对象时，会为对象分配存储空间，并调用相应构造器。构造器用于对象的创建和初始化。
* 成员变量的初始化总是在构造器初始化之前，不管成员变量和构造器声明的顺序如何。
* 与静态代码块对应的，有非静态代码块，只是非静态代码块在对象的每一次初始化时都会被调用！
* 当存在多个可变参数方法重载时，无法调用0个参数的该方法
    ```
    # 存在两个可变参方法的重载
    public void method(int... ints) {
        // do something
    }
    public void method(float... floats) {
        // do something
    }
    # 调用时，不可调0个参数的情况（尽管可变参数方法可以接受0个参数）
    obj.method();// not allowed! ambiguous call
    ```
* 创建 enum（枚举）类时，编译器会自动添加一些有用的特性（方法）：
    ```
    * toString(): 获得该枚举的名称;
    * ordinal(): 获得当前枚举对象在枚举类中的顺序（位置）,基于0;
    * static values(): 获得该枚举类中所有枚举对象的数组.
    ```
### Chapter 6 访问权限控制

### Chapter 7 复用类

### Chapter 9 接口
* 任何抽象性都应该是应真正的需求而产生的。当必须时，应该重构接口而不是到处添加额外级别的间接性，并由此带来的额外的复杂性。恰当的原则应该是优先选择类而不是接口。从类开始，如果接口的必须性变得非常明确，那么就进行重构。接口是一种重要的工具，但是它们容易被滥用。

### Chapter 10 内部类
* 当某个外部类的对象创建了一个内部类对象时，此内部类对象会秘密地捕获一个指向那个外部类对象的引用。在访问外部类的成员时，就是用那个引用来选择外部类的成员。
* 内部类的分类
    * **成员内部类**：处于外部类成员变量位置，该类可访问外部类的所有成员；外部类的方法中也可以创建内部类的对象。
    * **局部内部类**：处于外部类的方法中，属于方法的一部分，只能在该方法中访问内部类；该内部类也无法访问方法之外的外部类成员。
    * **作用域中的内部类**：处于某个方法中的某个作用域中（如if, while等），它会被一同编译，但是，出了作用域将无法被访问。
    * **匿名内部类**：以特定形式创建一个类的子类，它没有具体的名字。
    * **嵌套内部类**：用 static 修饰的内部类。它的作用类似于一个静态方法。
* **为什么需要内部类？**
    最主要的原因在于：内部类可以解决多重继承的问题。但我们通常不是说实现多个接口可以解决多重继承问题吗？个人认为，使用多个接口实现，如果存在相同的方法名，那么对于该重名方法，类在实现该方法后，这个重名方法只会存在一种实现，如下：

        public class SubClass implements Interface1, interface2 {
            public void method() {
                // 在本类中只能提供一种实现
            }
        }
        interface1 {
            void method();
        }
        interface2 {
            void method();
        }

    那么如果使用内部类来实现不同接口的重名方法，方式如下：

        public class SubClass implements Interface1 {
            public void method() {
                // 这里提供一种实现方式
            }
            public innerClass implements Interface2 {
                public void method() {
                    // 这里提供另一种实现方式
                }     
            }
        }
    可以说，内部类更完美地实现了“多重继承”。\
    内部类在事件驱动中可以扮演重要角色，不同的内部类继承“基础事件”后实现不同的功能。\

### Chapter 11 持有对象
* Collection 的构造方法中可以接受另一个 Collection，用它进行初始化，那么我们可以使用 Arrays.List() 方法生成 Collection 构造的参数。**但是**，Collection.addAll() 方法性能更好，可以通过构建一个空的 Collection, 然后调用 Collections.addAll() 进行填充, 为什么不调用 Collection.addAll() 方法呢？ 因为该方法只接受一个 Collection 参数，而 Collections.addAll() 接受可变长度的参数列表。
* ```Arrays.asList(T... a)```返回一个由数组支持的固定长度的```List```,对于该```List```的操作将直接修改底层数组,因此无法支持对集合元素的添加和删除。同时，在使用该方法时，可变参数列表中的类型需要注意：如果参数列表中的所有参数都是泛型类子类的子类，则需要使用“显示类型参数说明”提示编译器：

        // 定义类
        class Fa {}
        class Son1 {}
        class GraSon1 extends Son1 {}
        class GraSon2 extends Son1 {}

        public class AsListTest {
            public static void main(String[] args) {
                // 编译不通过，提示 Required List<Fa>, Found List<Son1>
                // List<Fa> list = Arrays.AsList(new GraSon1(), new GranSon2());
                // 需要提供“显示类型参数说明”
                List<Fa> list = Arrays.<Fa>AsList(new GraSon1(), new GranSon2());
            }
        }
* List.subList(fromIndex, toIndex) 方法返回原 list 的一个片段，片段范围 (fromIndex, toIndex]，该片段是一个 SubList 对象，这个对象持有了原 list 的一个引用，通过偏移量（offset）和片段的范围（size = toIndex - fromIndex）构成了原 list 的部分片段的“假象”，实际上对于 subList 的操作都是**直接对于原 list 的操作！**

        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
        List<Integer> subList = list.subList(1, 5);// 包括索引1，不包括索引5，即：[2, 3, 4, 5]
        // 打乱 subList 顺序
        Collections.shuffle(subList);
        System.out.println("打乱后的subList：" + subList);// 打乱后的subList：[4, 5, 2, 3]
        System.out.println("没动过的list：" + list);// 没动过的list：[1, 4, 5, 2, 3, 6, 7, 8]
   
* Queue 队列是一个典型的先进先出（FIRST-IN-FIRST-OUT, FIFO）容器。PriorityQueue 通过元素内置的比较器，或者传入一个比较器改变队列的优先级。
* Collection 和 Iterator 接口都对底层的具体容器进行了解耦，在迭代集合时，可以不必考虑具体容器的迭代方式。Collection 接口有许多方法，直接实现该接口比较繁琐，可以通过继承 AbstractCollection 类，实现 Iterator() 和 size()方法即可，但是需要考虑到 java 语言是单一继承的，想要使用继承时可能有一些限制，那么此时通过实现 Iterator 接口是比较灵活的，需要手动实现 hasNext(),next() 和 remove() 方法。
* JDK 1.5 引入了 Iterable 接口，实现了该接口的类都可以使用 foreach 语句。foreach 语句只能对数组 和 Iterable 使用
   

### Chapter 21 并发

* Java SE5 的 java.util.concurrent 包中的执行器(Executor)用于管理 Thread 对象，简化了并发编程。ExecutorService（具有服务生命周期的Executor）知道如何构建恰当的上下文来执行 Runnable 对象。

            // 使用 Executors 的静态方法创建 ExecutorService
            ExecutorService fixedThreadPool = Executors.newFiexedThreadPool(10);// 创建固定线程数的线程池
            ExecutorService cachedThreadPool = Executors.newCachedThreadPool();// 根据任务数量在线程池中动态创建线程
            ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(3);// 创建可执行定时任务的线程池
            ExecutorService singleThreadScheduleExecutor = Executors.newSingleThreadExecutor();// 创建不可配置的单一线程（如果任务未执行完成时线程死亡，则会创建新线程继续执行任务）
            ScheduleExecutorService singleThreadScheduleExecutor = Executors.newSingleThreadScheduledExecutor();// 创建可执行定时任务的单一线程
        
* Executors 的静态方法帮助我们方便地创建了线程池，其实它都是通过 ThreadPoolExecutor 对象，配置相应参数来创建线程池。我们可以通过定制参数参数来创建 ThreadPoolExecutor 对象：

            ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
                    100,                            //corePoolSize 核心线程数，当池中线程数量未达到核心线程数，接受到任务时将直接创建新线程
                    200,                            //maximumPoolSize 最大线程数，根据下方配置的拒绝策略，超出最大值后做出响应
                    60L,                            //keepAliveTime 存活时间，若线程数超过核心线程数，则超过部分县城的空闲时间大于存活时间会被终止
                    TimeUnit.SECONDS,               //unit 时间单位
                    new ArrayBlockingQueue<Runnable>(1000, true),   //workQueue 工作队列，当新任务进来时，核心线程都不处于空闲，则该任务被加入队列
                    Executors.defaultThreadFactory(),               //threadFactory 线程工厂，表明使用该工厂来创建线程
                    new ThreadPoolExecutor.AbortPolicy()            //rejectedExecutionHandler 拒绝策略，当工作队列排满，且线程数超过最大线程后的应对策略
                    );

* 执行 Runnable 任务没有返回值，想要任务执行返回相应值，可以执行 Callable 任务。Callable 定义了一个 call() 方法,通过调用 call() 方法返回一个任务的返回值。ExecutorService 通过调用 submit() 方法执行任务，返回一个 Future 对象。通过 Future 的 get() 方法获得返回值，注意，在 任务执行结束前，调用 get() 是一直阻塞的，可通过 isDone() 方法判断任务是否执行完成。

            // 创建 callable 任务
            class CallTask implements Callable<String> {

                @Override
                public String call() throws Exception {
                    return "the String from callable task";
                }
            }

             public class Test {
                public static void main(String[] args) {
                    ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
                    Future<String> future = cachedThreadPool.submit(new CallTask());
                    while (future.isDone()) {
                        System.out.println(future.get());
                    }
                }
             }

* Java SE 5 引入了更加显示的 sleep()，通过 TimeUnit 指定时间单元再调用 sleep() 方法，原来是通过 Thread 的静态方法 sleep():

            // before jdk 1.5
            Thread.sleep(1000);// 单位毫秒

            // after jdk 1.5 we can use
            TimeUnit.SECONDS.sleep(2);
        
* 通常，在一个线程中抛出的异常是无法在另一条线程中捕获的。JDK 1.5 引进了 Thread.UncaughtExceptioinHandler。通过在线程中设置未捕捉异常处理器，那么在另一个线程中可以捕获从该线程中逃逸的异常。

            // 创建未捕捉的异常处理器
            class MyHandler implements Thread.UncaughtExceptionHandler {
                public void uncaughtException(Thread t, Throwable e) {
                    System.out.println("caught exception :" + e);
                }
            }
            // 创建线程工厂
            class HandlerThreadFactory implements ThreadFactory {
                public Thread newThread(Runnable r) {
                    Thread t = new Thread(r);
                    System.out.println("created " + t);
                    // 设置异常处理器
                    t.setUncaughtExceptionHandler(new MyHandler());
                    return t;
                }
           }

           public class UncaughtExceptionThreadTest {
                ExecutorService uncaughtExceptionThreadPool = Executors.newCachedThreadPool(new HandlerThreadFactory());
                // uncaughtExceptionThreadPool.execute(some task);
           }
        
        
* 在并发情况下，将类的字段设置为 private 是非常重要的，否则即使加了锁，其他线程可以不通过同步的方法就可以直接改变字段的值，从而产生并发问题。

* 一个首先获得到锁的任务可以多次获得对象的锁。在一个加锁的方法中调用了另一个加锁的方法，那么该任务就会获得该对象的多个锁，锁的计数会增加，当任务每离开一个加锁方法，计数器减一，当计数器减为0时，其他任务才能去获取这个对象的锁。

* ReetrantLock, 使用它可以显示地进行加锁，并且相比 synchronized 关键字，ReetrantLock 可以进行更细粒度的控制：

            ReentrantLock lock = new ReentrantLock();
            // 尝试在3秒钟之内获取锁
            boolean isGot = lock.tryLock(3, TimeUnit.SECONDS);
            try {
                if (isGot) 
                    // 加锁
                    lock.lock();
                else 
                    // 没获取到，做其他事情
                
            } catch(InterruptedException e) {
                
            } finally {
                // 必须在finally块中释放锁
                if (isGot)
                    lock.unlock();
            }
            // 如果当前线程没有获取到锁，但对锁进行释放操作，会抛出 IllegalMonitorStateException
            
* 原子性操作表示一个或一组不可分割的操作，如果操作开始进行，那么一定会在上下文切换之前完成操作。对于 long 和 double 这种占 64 位字节的基本数据类型，JVM 在读取和写入时将其当作两个 32 位操作进行，因此其中存在上下文切换的可能，所以对于 long 和 double 的操作并不是原子性的！通过使用 volatile 关键字可保证上述操作的原子性。因为 volatile 保证了线程间的可见性，一旦对某个 volatile 字段进行了写操作，那么所有的写操作都能看见这种变化。

* 不要轻易地使用原子性和可见性去进行无锁编程，这是存在很大风险的！

* 原子类 位于 java.util.concurrent.atomic 包下，这个原子类提供了“机器”级别的原子性，举例 AtomicInteger 的 addAndGet 方法底层调用的是本地操作系统类库中的 compareAndSwapInt 来实现操作的原子性。

* 调用 sleep() 和 yield() 方法并不会释放锁，wait() 方法的调用会释放锁，这意味着其他同步方法在该方法的 wait 期间可以被其他线程访问。其中 wait，notify 和 notifyAll 方法只能在同步方法或同步块中调用，因为它们需要释放对象的锁，如果在其他非同步代码中调用，虽然没有编译报错，但是在运行期会抛出 IllegalMonitorStateException 异常。

* 除了相互持有对方的锁并等待对方锁释放引起的死锁外，线程的无期限 wait 也会造成死锁：

            // 信号错失演示
            boolean flag = true;
            Object lock = new Object;
            // method1
            public void method1() {
                while (flag) {
                    synchronized(lock) {
                        lock.wait();
                    }
                }
            }
            // method2
            public void method2() {
                synchronized(lock) {
                    flag = false;
                    lock.notify();
                }
            }
            
在上面这个例子中，如果 method1 中 while 判断条件通过后，执行权被另一条线程获取，此时 lock 的锁被执行 method2 的线程获取，并进行唤醒。其后 method1 继续执行，但是在这个时候执行 method1 的线程无法意识到条件已经发生改变，继续执行，导致 wait() 方法无法被唤醒导致死锁。method1 应该如下处理：
            
            // method2
            synchronized(lock) {
                while(flag) {
                    lock.wailt();
                }
            }



















