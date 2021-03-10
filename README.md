# secretweapon 秘密武器
在Java开发之路上的知识集合。
Knowledges collection involving any aspects of Java development.

## 网络基础
### TCP/IP协议

OSI七层参考模型与TCP/IP体系结构，以及参照前两者优点的五层协议。

| OSI体系结构  |                 TCP/IP体系结构                  | 五层协议结构  |
| :----------: | :---------------------------------------------: | ------------- |
|   7.应用层   |                                                 |               |
|  6. 表示层   | 应用层（各种应用层协议如：TELNET，FTP，SMTP等） | 5.应用层      |
|   5.会话层   |                                                 |               |
| 4.传输控制层 |                                                 | 4.传输控制层  |
|   3.网络层   |                    网际层IP                     | 3. 网络层     |
| 2.数据链路层 |                   网络接口层                    | 2. 数据链路层 |
|   1.物理层   |                                                 | 物理层        |

基于TCP/IP协议，只需要关心运输层以上的部分即可。因为计算机内核程序（kenel）中已经默认实现了运输层，网络层，数据链路层以及物理层。
#### 三次握手
TCP是一个面向连接，可靠的传输协议
- 连接：
1. 客户端传输控制层产生一个sync信号的数据包，发送给服务端。
2. 服务端收到客户端发送的sync信号的数据包，返回一个sync+ack的数据包给客户端。
3. 客户端收到服务端发送的sync + ack的数据包，返回一个ack信息的数据包给服务端。<br/>
至此三次握手已经完成，只有三次握手完成之后，双方才能为本次会话开辟资源（创建出线程或者fork出进程）。双方把用于本次会话的资源支起，能够响应后续对方发送的数据。<br/>
以上整个过程称为连接。

- 可靠：
不是使用两次握手，而是采用三次握手；三次握手确保了建立连接的时刻客户端与服务端双向的链路通畅以及收发能力。
#### 四次挥手
  在计算机内核当中，将传输控制层及以下的部分，是按照套接字（Socket）的方式处理的。
  单个Socket套接字信息包含：1. 发送方的ip + port； 2. 接收方的ip + port<br/>

  在日常开发过程中一般不会太关注发送方接收方的ip+port信息。其实在内核中已经帮我们对应好了发送与接收方的信息。<br/>
  运行的程序都需要指定一个端口号，与本机的端口号（一台计算机最多可以拥有2^16-1=65535个端口号）建立起map映射关系，才能通过网络进行数据的收发。<br/>
  在操作系统中有大量的数据流通，内核就是根据信息记录的端口号来决定从本机什么应用当中取信息，发送到何处，以及接收到的信息从何处来，到本机哪个应用中去的。因此在开发过程中遇到过端口号被占用的情况，其原因就是端口号必须是某一个程序的唯一表示。<br/>
  当同一个客户端与同一台服务器建立多个连接，也是因为有了套接字的两方ip + port信息的存在，所以多个连接之间互不干扰，都是互相独立的。<br/>
  当一台计算机与同一台服务器建立两个ssh连接时，服务端使用命令<br/>
```bash
bash netstat -natp
```
  参数：n表示不用类似localhost类的字符代替IP，即显示完整IP；a代表所有；t代表tcp连接；p代表显示进程PID。<br/>
  查看到的信息：<br/>
  // 图片待上传
 一台计算机的端口号最多为65535个，因此也是数据有限的资源。在使用完毕之后，应该关闭端口。而方式，则是四次挥手。<br/>
为什么关闭是四次？<br/>
TCP是面向连接的，连接双方都应该确认关闭，而后各自释放对应资源。<br/>
挥手过程（挥手的起点可以为客户端或服务端的任意一方）：
1. 挥手（结束）发起方发送一个fin数据包给接收方；
2. 接收方收到fin数据包之后，立刻发送一个ack数据包告诉发起方自己已经接收到了断开连接请求；
3. 接收方在完成自己的本次会话的信息传输之后，进入可以断开连接的状态，然后向断开连接的发起方发送一个fin数据包表示可以断开请求了。
4. 发起方在收到接收方发出的fin数据包之后，也立即发送一个ack数据包告诉接收方，自己也接收到了对方的结束连接请求。
而后双方按照自己的资源释放策略，在合适的时候各自释放本次连接所需要的资源。

## 数据结构&算法

## 设计模式

## Java基础

### io

### 锁

### 线程生命周期

NEW：新创建

RUNNABLE：可运行

BLOCKED：被阻塞

WAITING：等待

TIMED_WAITING：计时等待

TERMINATED：被终止

### 两种线程模型

### 线程池

#### **ThreadPoolExecutor**

所有线程池都调用了这个类

ThreadPoolExecutor(int corePoolSize 核心线程数,
                          int maximumPoolSize 最大线程数,
                          long keepAliveTime 最大存活时间,
                          TimeUnit unit 时间单位,
             BlockingQueue<Runnable> workQueue 队列,
                  ThreadFactory threadFactory   线程工厂,
           RejectedExecutionHandler handler    拒绝策略)

#### **ScheduledThreadPoolExecutor**(定长线程池)

创建一个定长线程池，支持定时及周期性任务执行

ScheduledThreadPoolExecutor(int corePoolSize) {
    super(	corePoolSize, //核心线程数,就是你传的参数 

​	Integer.*MAX_VALUE* , // *最大线程数*(2^31)-1

​		0 , //存活时间

​	*NANOSECONDS* ,//时间单位*纳秒*
       new DelayedWorkQueue()); //优先级队列

#### **newCachedThreadPool** **(可缓存线程池)**

可缓存线程池，先查看池中有没有以前建立的线程，如果有，就直接使用。如果没有，就建一个新的线程加入池中，缓存型池子通常用于执行一些生存期很短的异步型任务

newCachedThreadPool() {
    return new ThreadPoolExecutor(

0, //核心线程数

Integer.*MAX_VALUE*,//最大线程数 2^31 -1

 60L,//存活时间

 TimeUnit.*SECONDS*,//时间单位
  new SynchronousQueue<Runnable>()); //同步队列

#### **newFixedThreadPool**(可重用固定个数线程池)

创建一个可重用固定个数的线程池，以共享的无界队列方式来运行这些线程

newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(

nThreads, //核心线程数

nThreads,//最大线程数
 0L, //存活时间

TimeUnit.*MILLISECONDS*,//时间单位
 new LinkedBlockingQueue<Runnable>());//链表缓冲队列

#### **newSingleThreadExecutor**(单例线程池/单一线程池)

创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

 newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(

1, //核心线程数

1,//最大线程数
   0L,//存活时间

 TimeUnit.*MILLISECONDS*,//时间单位                                

new LinkedBlockingQueue<Runnable>()));//链表缓冲队列

#### 提交优先级和执行优先级

##### 提交优先级

核心线程——队列——非核心线程

##### 执行优先级

核心线程——非核心线程——队列

### 几种常用队列

#### **LinkedBlockingQueue**

 LinkedBlockingQueue大小不固定的BlockingQueue，若其构造时指定大小，生成的BlockingQueue有大小限制，不指定大小，其大小有Integer.MAX_VALUE来决定。其所含的对象是FIFO顺序排序的

#### **BlockingQueue**

#### **ArrayBlockingQueue**

ArrayBlockingQueue（int i）:规定大小的BlockingQueue，其构造必须指定大小。其所含的对象是FIFO（先进先出）顺序排序的

#### *DelayedWorkQueue*

DelayedWorkQueue 阻塞队列

成员变量

// 初始时，数组长度大小。

private static final int INITIAL_CAPACITY = 16;        

// 使用数组来储存队列中的元素。

private RunnableScheduledFuture<?>[] queue = new RunnableScheduledFuture<?>[INITIAL_CAPACITY];        

// 使用lock来保证多线程并发安全问题。

private final ReentrantLock lock = new ReentrantLock();        

// 队列中储存元素的大小

private int size = 0;        

//特指队列头任务所在线程

private Thread leader = null;          

// 当队列头的任务延时时间到了，或者有新的任务变成队列头时，用来唤醒等待线程

private final Condition available = lock.newCondition();

#### 

## JVM
### 记忆集在gc回收中的作用

### GC垃圾收集器

#### 对象存活判断
判断一个对象是否存活一般有两种方法
1. 引用计数：每个对象有一个引用计数属性，表示当前对象被引用数，计数为0时可以回收，此方法简单，但是无法解决对象循环引用的问题。
2. 可达性分析：从GC Roots开始向下搜索，搜索所走过的路径成为引用链，当一个对象到GC Roots没有任何引用脸相连时，则标记该对象是不可达的。
  而不可达对象距离被回收还有如下过程：
  Step1 判断对象是否有必要执行finalize()方法，当对象没有覆盖finalize()方法或者虚拟机已经执行过finalize()方法，则判断为“没有必要执行”。
  Step2 如果对象被认为有必要执行finalize()方法，那么该对象的finalize()方法将会被放置在一个名为F-Queue的队列之中。稍后JVM自动创建一个低优先级的finalizer的线程去尝试触发方法。
  Step3 对象如果在finalize方法期间被发现与任一引用链上的对象建立的关联，就会被移出“即将回收”集合。<br/>
  
  
#### 垃圾回收算法
##### 标记-清除算法
最基础的算法，后续的算法都是由此改进的，分为标记，清除两个步骤：
1. 首先标记出需要被回收的对象。
2. 然后对所有被标记的对象进行回收。<br/>
缺点：
- 效率：标记和清除的效率都不高。
- 空间：在清除之后内存中会产生大量的碎片，后续程序运行中很可能因为连续空间不足而不是实际可用空间不足而提前触发另一次收集动作。
##### 复制算法
复制算法可以解决标记-清除算法的效率问题，步骤：
1. 将可用内存按容量划分成大小相等的两块，每次只使用其中的一块；
2. 当一块的内存用完时，就把还活着的对象复制到另一块上面；
3. 然后再把已使用的内存一次性清楚干净。
这样使得每次都是对其中的一块内存回收，内存分配时也不用考虑内存碎片问题，只要移动堆顶指针即可。
缺点：
- 空间：可用的内存为原来的一半。
##### 标记整理算法
复制算法在对象存活率较高时就要执行较多的复制操作，效率较低。重点是为了应对100%存活的极端情况，必须有像HotSpot虚拟机中幸存区-老年代这样另一块内存空间对自身进行分配担保。而老年代显然不能直接使用复制算法。
标记-整理算法是根据老年代的特征而产生的算法，步骤：
1. 标记出所有需要被回收的对象。
2. 将存活的对象一端移动。
3. 清理存活边界以外的内存。
##### 分代收集算法
当前商业虚拟机都采用这种算法回收。并没有引入新的回收逻辑，而是根据对象的存活周期把内存划分成几块，一般是新生代和老生代。这样就可以根据各个年代的特点采用合适的算法进行垃圾回收。
- 在新生代中，对象存活周期较短，朝生夕死，采用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。
- 在老年代中，对象的存活率较高，没有额外的空间对它进行分配担保，必须使用“标记-清除”或“标记-整理”算法，
#### 垃圾收集器

### jvm内存模型

![1615372659557](D:\secretweapon\1615372659557.png)

程序计数器：

较小的内存空间， 当前线程执行的字节码的行号指示器；各线程之间独立存储，互不影响；

 java 栈：

线程私有，生命周期和线程，每个方法在执行的同时都会创建一个 栈帧用于存储局部变量表，操作数栈，动态链接，方法出口等信息。方法的执行就对应着栈帧在虚拟机栈中入栈和出栈的过程；栈里面存放着各种基本数据类型和对象的引用；(简单来说就是方法放在里面)

 本地方法栈：

本地方法栈保存的是native方法的信息，当一个JVM创建的线程调用native方法后，JVM不再为其在虚拟机栈中创建栈帧， JVM只是简单地动态链接并直接调用native方法；(简单来说就是放本地(c++/第三方)方法的,比如sdk包)

 堆：

Java堆是程序员需要重点关注的一块区域，因为涉及到内存的分配(new关键字，反射等)与回收(回收算法，收集器等)；(对象,字符串常量池)

 方法区/元空间：

也叫永久区，用于存储已经被虚拟机加载的类信息，常量("zdy","123"等)，静态变量(static变量)等数据。

 (jdk1.8已经将方法区去掉了，将方法区移动到直接内存)(元空间,类放在里面)

 

 运行时常量池：

 运行时常量池是方法区的一部分，用于存放编译期生成的各种字面("zdy","123"等)和符号引用。(元空间一部分,基本类型常量池)

 

 直接内存：不是虚拟机运行时数据区的一部分，也不是java虚拟机规范中定义的内存区域；

### 虚拟机类加载机制

#### 类加载过程
类从被加载到虚拟机内存开始，到卸载出内存为止，整个生命周期包括：
1. 加载
2. 验证
3. 准备
4. 解析
5. 初始化
6. 使用
7. 卸载<br/>
  其中加载，验证准备和初始化和卸载这五个阶段的顺序是确定的，类的加载过程必须按照这种顺序按部就班地开始，而解析阶段则不一定；为了支持Java语言运行时绑定（也叫动态绑定或者晚期绑定）。需要注意一点，此处阐述的是这些阶段都是按照顺序“开始”，因为这些阶段通常都是互相交叉地混合式进行的，可能在一个阶段的执行过程中，调用，激活另一个阶段。
#### 双亲委派

![1615377295126](D:\secretweapon\1615377295126.png)

## 数据库

### mysql

#### **Innodb事务隔离级别**

读已提交,读未提交,可重复度,串行化

#### **特点**

#### 默认事务隔离级别,为什么?!**

可重复读,因为在5.0之前binlog只支持statement(记录修改的sql语句)这种模式,这种模式在

读已提交这个隔离级别下主从复制是有bug的

#### **可重复读这个事务隔离级别是怎么实现的?!**

乐观锁+快照?

Redo log,undo log?

#### **Mysql事务提交成功后还会丢失吗?**

会丢失,因为执行顺序是先缓冲到日志缓冲区logBuffer,然后mysql调用write写入操作系统缓冲区os cahe,然后再flush到硬盘。而mysql在写入系统缓冲区后就会认定已经提交成功,具体什么时候flush到硬盘,就和mysql没有关系了,如果这个时候操作系统崩溃,就会丢失数据。

#### **解决办法**

innodb_flush_log_at_trx_commit参数能够控制事务提交时，刷redo log的策略

**策略一：最佳性能**(innodb_flush_log_at_trx_commit=0)每隔一秒，才将Log Buffer中的数据批量write入OS cache，同时MySQL主动fsync。这种策略，如果数据库崩溃，有一秒的数据丢失。 

**策略二：强一致**(innodb_flush_log_at_trx_commit=1)每次事务提交，都将Log Buffer中的数据write入OS cache，同时MySQL主动fsync。这种策略，是InnoDB的默认配置，为的是保证事务ACID特性。 

**策略三：折衷**(innodb_flush_log_at_trx_commit=2)每次事务提交，都将Log Buffer中的数据write入OS cache；每隔一秒，MySQL主动将OS cache中的数据批量fsync。画外音：磁盘IO次数不确定，因为操作系统的fsync频率并不是MySQL能控制的。这种策略，如果操作系统崩溃，最多有一秒的数据丢失。

高并发业务，行业内的最佳实践，是：
innodb_flush_log_at_trx_commit=2

## Spring

### ioc执行流程

![1615373777134](D:\secretweapon\1615373777134.png)

创建beanFactory——加载配置文件或扫描注解读取bean的定义信息到beanDefinition，beanDefinition实现了beanDefinitionRader接口——执行实现了beanFactoryPostProcessor接口的类——通过反射的方式将类实例化到beanFactory——给类填充属性——在初始化之前执行实现了beanPostProcessor接口的类的before方法——初始化bean执行init方法——执行实现了beanPostProcessor接口的类的after方法——完整对象就创建完成了

### 三级缓存

## spring cloud

## mybatis

### 
