## 什么是JVM内存结构？

![image-20211029094056211](https://gitee.com/mayichidoufu/note-image/raw/master/image-20211029094056211.png)
jvm将虚拟机分为5大区域，程序计数器、虚拟机栈、本地方法栈、java堆、方法区；
-   程序计数器：线程私有的，是一块很小的内存空间，作为当前线程的行号指示器，用于记录当前虚拟机正在执行的线程指令地址；
    
-   虚拟机栈：线程私有的，每个方法执行的时候都会创建一个栈帧，用于存储局部变量表、操作数、动态链接和方法返回等信息，当线程请求的栈深度超过了虚拟机允许的最大深度时，就会抛出StackOverFlowError；
    
-   本地方法栈：线程私有的，保存的是native方法的信息，当一个jvm创建的线程调用native方法后，jvm不会在虚拟机栈中为该线程创建栈帧，而是简单的动态链接并直接调用该方法；
    
-   堆：java堆是所有线程共享的一块内存，几乎所有对象的实例和数组都要在堆上分配内存，因此该区域经常发生垃圾回收的操作；
    
-   方法区：存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码数据。即永久代，在jdk1.8中不存在方法区了，被元数据区替代了，原方法区被分成两部分；1：加载的类信息，2：运行时常量池；加载的类信息被保存在元数据区中，运行时常量池保存在堆中
## 什么是JVM内存模型？
Java 内存模型（下文简称 JMM）就是在底层处理器内存模型的基础上，定义自己的多线程语义。它明确指定了一组排序规则，来保证线程间的可见性。
这一组规则被称为 Happens-Before, JMM 规定，要想保证 B 操作能够看到 A 操作的结果（无论它们是否在同一个线程），那么 A 和 B 之间必须满足 Happens-Before 关系：单线程规则：一个线程中的每个动作都 happens-before 该线程中后续的每个动作监视器锁定规则：监听器的解锁动作 happens-before 后续对这个监听器的锁定动作volatile 变量规则：对 volatile 字段的写入动作 happens-before 后续对这个字段的每个读取动作线程 start 规则：线程 start() 方法的执行 happens-before 一个启动线程内的任意动作线程 join 规则：一个线程内的所有动作 happens-before 任意其他线程在该线程 join() 成功返回之前传递性：如果 A happens-before B, 且 B happens-before C, 那么 A happens-before C怎么理解 happens-before 呢？如果按字面意思，比如第二个规则，线程（不管是不是同一个）的解锁动作发生在锁定之前？这明显不对。happens-before 也是为了保证可见性，比如那个解锁和加锁的动作，可以这样理解，线程1释放锁退出同步块，线程2加锁进入同步块，那么线程2就能看见线程1对共享对象修改的结果。图片Java 提供了几种语言结构，包括 volatile, final 和 synchronized, 它们旨在帮助程序员向编译器描述程序的并发要求，其中：volatile - 保证可见性和有序性synchronized - 保证可见性和有序性; 通过**管程（Monitor）*保证一组动作的*原子性final - 通过禁止在构造函数初始化和给 final 字段赋值这两个动作的重排序，保证可见性（如果 this 引用逃逸就不好说可见性了）编译器在遇到这些关键字时，会插入相应的内存屏障，保证语义的正确性。有一点需要注意的是，synchronized 不保证同步块内的代码禁止重排序，因为它通过锁保证同一时刻只有一个线程访问同步块（或临界区），也就是说同步块的代码只需满足 as-if-serial 语义 - 只要单线程的执行结果不改变，可以进行重排序。所以说，Java 内存模型描述的是多线程对共享内存修改后彼此之间的可见性，另外，还确保正确同步的 Java 代码可以在不同体系结构的处理器上正确运行。

## 双亲委派模型

定义： 如果接收到加载类请求时，它不会直接去加载该类，还是把加载请求委托给父加载器进行加载，只有父加载找不到类才会用子类加载器。

![image-20211028194543046](https://gitee.com/mayichidoufu/note-image/raw/master/image-20211028194543046.png)

###  为什么需要双亲委派？

+ 保证安全

## 自定义类加载器

 ### 1.用处

+ 隔离加载类
  + 模块隔离，把类加载不同应用中，如tomcat定义多种类加载器进行隔离。
+ 修改类加载方式，除boostrap加载器其他加载不一定存在
+ 防止源码泄露
+ 扩展加载器

 ### 2.过程

![image-20211028200435228](https://gitee.com/mayichidoufu/note-image/raw/master/image-20211028200435228.png)

### 3.实现方式

  都需要继承classLoader类。

+ 重写 loadClass （可以破坏双亲委派）
+ 重新findClass （推荐）
