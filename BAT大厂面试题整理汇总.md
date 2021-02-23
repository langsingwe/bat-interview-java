> 1. 本文所有题目，皆节选自各大学习网站，基本的是BAT等一些大厂的面试题
> 2. 答案都是自己整理总结，会存在很多的错误之处，欢迎大家指正。有兴趣的同学也可以提交自己的回答
> 3. 文末会附上相关分享帖子的链接，感谢面经分享者
> 4. 码字不易，给个star
> 5. 尊重原创，禁止用于商业用途
[TOC]

# java

## StringBuffer和StringBuilder的区别

StringBuffer是线程安全的，StringBuilder线程不安全

## ArrayList和LinkList的区别

* ArrayList数据结构为数组，LinkList数据结构为链表
* ArrayList随机访问数据效率高，时间复杂度O(1)，添加和删除复杂度为O(n)；LinkList查找时间复杂度O(n)，添加删除为O(1)

## HashMap的实现原理

HashMap数据结构为数组+链表

将k和v封装到Node对象中，调用k的`hasCode()`方法得出 hash 值，通过哈希函数，将hash值转成数组的下标。如果数组该位置没有元素，就把Node数据放到这个位置上，如果位置上有链表，调用`equals()`方法比较k，如果`equals()`返回`false`，该Node添加到链表的末尾，返回`true`，该节点数据被v覆盖。

## HashMap扩容原理

扩容调用`resize()`方法

JDK7中，必须满足两个条件

1. 当存放新值的时候，已有元素的个数大于等于阈值
2. 发生hash碰撞

才会触发扩容，JDK8只需要满足条件1就可以触发扩容

初始化`HashMap`的时候，如果没有指定数组长度，默认数组长度为16。如果指定了数组长度，则会修改为2的倍数，负载因子0.75，因此在初始情况下，元素个数大于0.75 * 16 = 12时，会触发扩容，数组长度翻倍。扩容后，每个键值对数据存储的下标需要重新计算。通过公式keyHash&(newLength-1)，新的newIndex = oldIndex +扩容增加的长度。

JDK7使用的头插法，在多线程的情况下可能会造成死循环。JDK8采用的尾插法扩容，不会有这个问题

## HashTable 的扩容是线程安全的吗

是。put操作是线程安全，put的时候触发扩容`rehash()`

## hashmap的扩容为什么是2倍

扩容的时候会调用`e.hash & (newCap - 1)`把旧的元素添加到新的tab中，容量是2的n次方，n-1的二进制的所有位都是1，这样再和hash进行位运算的时候，能够充分的散列，使得添加的元素能够均匀的分布在数组上，避免哈希碰撞

## ConcurrentHashMap和HashMap的区别

1. `ConcurrentHashMap`线程安全。``HashMap`不是线程安全
2. 1.8之前`ConcurrentHashMap`数据结构为`segment`数组+数组+链表，1.8之后为数组+链表+红黑树。1.8之前`HashMap`数据结构为数组+链表，1.8之后为数组+链表+红黑树

## HashMap冲突怎么办

使用拉链法。所有冲突的元素，按照链表存储。

## java的深拷贝和浅拷贝

1. 浅拷贝：基础数据类型的修改互不影响，引用类型的修改会有影响
2. 深拷贝：对于引用类型，深拷贝会新建一个对象空间，然后拷贝里面的内容。所以他们的引用类型也指向不同的内存空间。所以不会互相影响。对于深拷贝，需要对每个子引用对象都实现`Cloneable`接口并重写`clone()`方法

## 说说对线程的理解

线程是任何执行和调度的最小单位

java里面可以通过集成`Thread`类，实现`Runnable`接口，或者实现`Callable`和`Future`接口创建线程

## java中如何创建线程

java里面可以通过集成`Thread`类，实现`Runnable`接口，或者实现`Callable`和`Future`接口创建线程

## synchronized和volatile区别，二者的实现原理

* volatile仅仅是变量级别的，synchronized可以作用在类，方法，变量上
* volatile只能保证变量的可见性，synchronized能保证可见性和原子性
* volatile不会造成线程阻塞，synchronized不会造成线程阻塞
* volatile本质是告诉jvm，当前变量存在不确定性，需要直接从主存中获取。synchronized是锁定当前变量，只有当前线程可以访问当前变量，其他线程被阻塞
* volatile不会被编译器优化。synchronized会被编译器优化

## synchronized修饰静态变量和非静态变量的区别

* 修饰静态变量是类级别的加锁，会给当前类的所有实例加锁
* 修饰非静态变量是实例级别的加锁，只会锁当前对象

## hashmap 和 hashtable 的区别

1. hashmap线程不安全，hashtable线程安全
2. hashmap允许null值，hashtable不允许null值
3. hashmap集成自`AbstrctMap`类，hashtable集成自`Dictionary`类
4. hashmap重新计算hash值，hashtable直接使用key 的`hasCode()`
5. hashmap默认容量为16，底层数组容量要求为2的倍数。hashtable默认容量为11，底层数组不要求容量为2的倍数，扩容时，容量变为2n+1

## hashtable 怎么保证线程安全的

方法上加了`synchronized`关键字

## synchronized 是怎么实现的，监视器锁是怎么实现的

synchronized 是怎么实现的

同步方法通过`ACC_SYNCHRONIZED`关键字隐式加锁。当线程要执行的方法被标注上`ACC_SYNCHRONIZED`时，需要先获取锁才能执行该方法

同步代码块通过`monitorenter`和`monitorexit`来加锁和释放锁。当线程执行到`monitorenter`时，需要先获取锁才能执行方法，当线程执行到`monitorexit`时，需要释放锁。每个对象自身维护一个被加锁次数的计数器，当计数器数字为0的时候表示可以被任意线程获得锁，当计数器不为0的时候，只有获得锁的线程才能再次获得锁，即可重入锁

监视器锁是怎么实现的

基于c++的`ObjectMonitor`实现的，几个关键属性
* `_owner`: 指向持有`ObjectMonitor`的线程
* `_WaitSet`: 存放处于`wait`状态的线程队列
* `_EntryList`： 存放处于等待锁状态的线程队列
* `_recursions`: 锁的重入次数
* `_count`: 用来记录线程获取锁的次数

当多个线程访问一段同步代码的时候，会先进入`_EntryList`队列，当某个线程获取到对象的`monitor`后进入`_Owner`区域并把`_owner`变量设置为当前线程，同时`monitor`的计数器`_count`加1。

若持有`monitor`的对象调用`wait()`方法，将释放当前持有的`monitor`，`_owner`变量置为`null`，`_count`减1，同时该线程进入`_WaitSet`集合中等待被唤醒。若当前线程执行完毕也将释放`monitor`并复位变量的值。

## ConcurrentHashMap 是怎么实现的

jdk1.7中采用的分段锁实现，将数据分成一段一段的存储，然后给每一段数据加锁，当一个线程访问其中一段的数据时，其他段的数据也能被其他线程访问

jdk1.8采用`CAS`和`synchronized`方式

[ConcurrentHashMap是如何实现线程安全的](https://blog.csdn.net/qq_41737716/article/details/90549847)

## ConcurrentHashMap说一下，怎么实现线程安全的，分段锁是怎么实现的

JDK1.7通过分段锁实现，JDK1.8通过CAS+synchronized实现

分段锁通过内部类`Segement`，通过变量count，基于AQS的原理实现

## CAS 的问题是什么

1. ABA问题。假如线程1在把值A修改成B的过程中，另一个线程B把值A修改成B又改成了A，线程1使用期望值A与当前变量进行比较时，发现A没有变，于是CAS将A进行交换，但是实际上A已经被其他线程改变过，这不符合乐观锁的设计思想
2. 资源消耗。如果自旋一直不成功，会一直占用CPU
3. 只能单变量

## CAS 适用场景
适用于资源竞争不是特别激烈的情况。使用`synchronized`频繁切换内核态和用户态浪费CPU资源。CAS基于硬件，不需要进入内核，不需要线程切换

## ReentrantLock的特点

* 可重入
* 可中断
* 可限时
* 公平锁

## 公平锁和非公平锁

* 公平锁：线程按照申请锁的顺序获得锁
* 非公平锁：线程申请锁的时候，直接尝试获取，获取不到，进入等待队列

##  AQS的实现原理，包括其中的cas，cas是怎么实现的，有什么好处？

* AQS实现原理：如果请求的资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设定为锁定状态。如果被请求的资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，AQS中使用CLH队列锁实现，将暂时获取不到锁的线程加入到队列中

* CAS实现原理：内存中存在三个值：内存位值，预期原值和新值，如果内存位置的值和预期原值相等，那么处理器会将该位置值置为新值，否则不做任何操作。CAS是乐观锁，没有显示的进行加锁，降低了系统的开销，节约资源。

## Object用到了哪些方法

* `clone`：实现对象的浅复制，只有实现了`Cloneable`接口才能调用本方法
* `getClass`：获取运行时的类型
* `toString`：打印对象的字符串，默认打印内存地址
* `finalize`：释放资源
* `equals`：判断对象是否相等，在Object类中==和`equals()`一样
* `hashCode`，对象的哈希地址
* `wait`：当前线程进入睡眠状态
* `notify`：唤醒在对象中的某个线程
* `notifyAll`：唤醒该对象上等待的所有线程

## Object线程同步的相关方法

`wait()、notify()、notifyAll()`

## jdk的动态代理是怎么实现的

静态代理是指在程序运行前，就已经存在的编译好的代理类是静态代理，实现静态代理的步骤

1. 定义业务接口
2. 被代理类实现业务接口
3. 代理类实现业务接口
4. 调用

静态代理是在程序运行期间，根据需要动态创建代理类来完成具体功能，动态代理分为jdk动态代理和cglib代理两大类

jdk动态代理创建步骤

1. 创建被代理的接口和类
2. 创建`InvocationHandler`接口的实现类，在`invoke`方法中实现代理逻辑
3. 通过Proxy的静态方法`newProxyInstance( ClassLoaderloader, Class[] interfaces, InvocationHandler h)`创建一个代理对象
4. 使用代理对象

## 说说类加载器，双亲委派了解吗？

启动类加载器（Bootstrap ClassLoader）、扩展类加载器（Extension ClassLoader）、应用程序类加载器（Application ClassLoader）、自定义类加载器（Customer ClassLoader）

双亲委派：在某个类加载器加载class文件时，他首先委派自己的上级加载这个类，一次传递到启动类加载器，如果顶层查找不到，再到子层查找

## 说说对JVM的了解

jvm是java虚拟机，通过jvm，java实现了跨平台，java语言在不同平台不需要重新编译，只需要部署jvm就可以了。

## 为什么说Java是跨平台的

java文件编译后生成class文件，运行在jvm上，在不同平台不需要重新编译，只需要部署jvm

## JVM的组成？每一块的作用

1. 类加载器：加载class文件到运行时数据区
2. 执行引擎：执行class文件中的指令
3. 本地库接口：调用其他语言的接口
4. 运行时数据区：程序加载运行的地方

一般我们说jvm的组成主要就是指运行时数据区，运行时数据区分为

1. 程序计数器：有两个作用：实现代码的流程控制，用来记录当前线程执行的位置
2. jvm栈：执行java字节码服务
3. 本地方法栈：执行native方法服务
4. 堆：存放对象的内存空间
5. 方法区：存放常量、静态变量、加载后的类信息、运行时常量池

## 垃圾回收GC分代和GC算法

GC分为新生代、老年代、元数据区。其中，新生代又分为伊甸区（Edgen）、From区、To区

GC算法有：标记-清除算法、复制算法、标记-整理算法、分代收集算法

## cms和G1的区别

1. 使用范围不一样：CMS是老年代的收集器，G1是新生代和老年代
2. STW时间：CMS是以最小STW为目标的收集器，G1可预测GC的STW
3. 垃圾碎片：CMS使用标记-清除算法，会产生内存碎片。G1使用标记-整理算法，降低了内存碎片
4. 回收过程不一样：CMS为初始标记-并发标记-重新标记-并发清除。G1为初始标记-并发标记-最终标记-筛选回收

## 什么样的对象会在堆里

被实例化的对象，没有被GC的

## StackOverFlow有遇到过吗？说一下

一般是代码问题，循环递归调用太多次了，导致栈帧占满了-Xss设置的值




# spring

## IOC是怎么解决循环依赖的

spring创建对象的时候使用三级缓存，`singletonObjects`、`earlySingletonObjects`、`singletonFactories`，A创建的时候，实例化A放到缓存中，初始化A的时候，依赖注入B，此时去容器获取B，发现B不存在，B开始创建，先实例化放入缓存，然后初始化B，B初始化需要依赖注入A，此时A已经被实例化并放入缓存中了，所以可以获得A的实例。B初始化成功，A也初始化成功。

https://cloud.tencent.com/developer/article/1497692

## AOP是怎么实现的

spring 采用 jdk 动态代理实现 AOP

1. 将切面使用动态代理的方式动态织入到目标对象，形成一个代理对象
2. 目标对象如果没有实现代理接口，spring 会使用CGLib生成代理对象

## 注解是怎么实现的

1. 使用ASM技术扫描.class文件，把包含注解的bean注册到beanFactory中
2. 然后spring再注册处理器
3. 实例化处理器，并将其注册到容器的beanPostProcessor列表中
4. 创建bean的过程中，属性注入或者初始化bean时会调用对应的注解处理器进行处理

## 介绍下Spring，Spring是怎么实现的

spring是java最常用的框架之一，spring最大的特点就是IOC和AOP，spring使用bean工厂，将对象的创建交给spring容器，不需要自己手动去new对象，使用动态代理实现AOP，把一些和业务无关的功能，比如日志等操作，降低系统的耦合性

# 设计模式

## 常用的设计模式

模板方法、策略模式、工厂模式、单例模式

## 单例的实现方式

懒汉式

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (null == instance) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

线程安全的懒汉式

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static synchronized Singleton getInstance() {
        if (null == instance) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

双重校验锁法

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static synchronized Singleton getInstance() {
        if (null == instance) {
            synchronized (Singleton.class) {
                if (null == instance) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

```



# MySQL

## mysql数据结构

B+树

## 事务的隔离级别

1. RU 读未提交
2. RC 读已提交
3. RR 可重复读
4. serializable 串行化

## 索引的分类

1. 普通索引：最基本的索引，没有任何限制
2. 唯一索引：索引列的值必须唯一，允许有空值
3. 主键索引：特殊的唯一索引。不允许空值
4. 组合索引：多个字段创建的索引，遵循最左匹配原则
5. 全文索引：用大文本对象列构建的索引

##  说说建索引的原则

1. 对查询频率高的字段建索引
2. 对排序、分组、联合查询频率高的字段建索引
3. 索引的数目不宜过多，会增加资源开销，降低insert/update/delete的执行效率
4. 选择唯一索引
5. 尽量选择数据量小的字段作为索引
6. 数据量小的表尽量不要使用索引
7. 尽量使用前缀索引
8. 定期清除不再使用和使用很少的索引

## 索引优化

使用`explain`方法查看结果，分析结果进行索引优化

## 索引失效

1. `like`以`%`开头
2. `or`查询，必须左右字段都是索引，否则索引失效
3. 联合索引，遵从最左匹配原则，如果不是使用第一列索引，索引失效
4. 数据出现隐形转换，如`varchar`字段没加单引号，自动转为`int`类型，会使索引失效
5. 索引字段使用`not`、`<>`、`!=`，索引失效
6. 索引字段使用函数，索引无效

## 说说索引数据结构

索引使用B+树，非叶子节点存储关键字和指向子节点的指针，叶子节点存储关键字和数据，多个叶子节点之间用链表连接。

## 为什么索引要用B+树，不用B树

* B+树非叶子节点只存储关键字和指向子节点的指针，而B树还存储了数据，在同样大小的情况下，B+树可以存储更多的关键字
* B+树叶子节点存储了所有关键字和数据，并且多个节点用链表连接。可以快速支撑范围查找
* B+树非叶子节点不存储数据，所以查询时间复杂度固定为`O(logN)`，B树查询时间复杂度不固定，最好是`O(1)`

## mysql为什么用B+树不用红黑树

B+树不是平衡二叉树，红黑树是平衡二叉树，数据很大的时候，树的深度会很大，磁盘IO操作会很频繁

## 说说mysql的约束

1. `not null`，非空约束：字段不能为空，必须给一个默认值
2. `unique`，唯一约束：字段值不允许重复，可以有多个null值
3. `primary key`，主键约束
4. `foreign key`，外键约束
5. `check`，检查约束，mysql里面可以用，但不生效

## mysql 如何保证某个值一天更新一次

1. 新建一个存储过程，更新某个值
2. 新建一个定时任务`CREATE EVENT`，时间为1天，定时调度这个存储过程

##  索引这么多优点，为什么不对表中的每一个列创建一个索引呢?

1. 索引虽然会增加查询效率，但是会降低 `insert/update/delete`的效率
2. 索引文件不是全部在内存里，也会存在磁盘里，频繁的磁盘I/O很消耗系统资源
3. 针对数据量小的表，通过查询索引的方法，可能还不没有直接查询的速度快
4. 大量的创建索引，也会增加维护成本

## 说说慢查询

慢查询是mysql的慢查询日志，用来记录mysql中响应时间超过设定阈值的语句。默认是关闭的，如果不是调优需要，尽量不要开启。因为生成日志文件需要消耗资源。

造成慢查询的原因可能有：

1. 没有设置索引或没有使用到索引
2. 网络问题
3. I/O吞吐量过小
4. 内存不足
5. 查询的数据量过大
6. 锁或者死锁
7. 查询语句存在问题，需要优化等

解决方法有：

1. 把数据、日志、索引放到不同的I/O设备上，增加读取速度
2. 纵向、横向分割表，减少表的尺寸
3. 升级硬件
4. 根据查询条件，建立索引，索引优化
5. 提高网速
6. 扩大服务器内存
7. 分库分表

## mysql死锁怎么处理

1. 等待直到超时，事务回滚
2. 发起死锁检测，主动回滚一条事务



# NoSQL

## 缓存与数据库双写，不一致问题及解决方案

1. 设置过期时间，定时清理缓存
2. 最经典和简单的方法是缓存+数据库读写模式。
   * 读的时候，先读缓存，缓存没有，查询数据库，加到缓存中
   * 更新的时候，先删除缓存，再更新数据库

2. 复杂情况下：更新，先删除缓存，此时另一个读请求过来，发现缓存是空，读取数据库，把修改前的旧数据加入到了缓存中，随后新的数据保存成功，缓存不一致。
   * 读写分离：写请求修改数据库和缓存是一个事务，保证强一致性
   * 使用消息队列：写请求放到一个队列中，写请求处理成功，从队列中删除。此时但凡有读请求，到队列中查询，如果队列有对应的写请求，读请求也放到队列中



# 算法

## 数组的快排

```java
//排序
public static void sort(int[] arr, int lo, int hi) {
  if(lo >= hi) {
    return;
  }
  int index = partition(arr, lo, hi);
  sort(arr, lo, index-1);
  sort(arr, index+1, hi);
}

//三数取中切分
public static int partition(int[] arr, int lo, int hi) {
  int mid = lo + (hi - lo) / 2;
  if(arr[mid] > arr[hi]) {
    swap(arr, mid ,hi);
  }
  if(arr[lo] > arr[hi]) {
    swap(arr, lo ,hi);
  }
  if(arr[mid] < arr[lo]) {
    swap(arr, mid, lo);
  }
  int key = arr[lo];
  while(lo < hi) {
    while(arr[hi] >= key && hi > lo) {
      hi--;
    }
    arr[lo] = arr[hi];
    while(arr[lo] <= key && hi > lo) {
      lo++;
    }
    arr[hi] = arr[lo];
  }
  arr[hi] = key;
  return hi;
}

//交换
public static void swap(int[] arr, int i ,int j) {
  int temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```

## 已知域名数组，输入域名，判断是否在数组中

```java
public static boolean find(String[] arr, String key) {
    Arrays.sort(arr);
    int index = Arrays.binarySearch(arr, key);
    if (index >= 0) {
        return true;
    }
    return false;
}
```

## 给定一个正整数 n，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵

[螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

思路：

* 生成一个 n×n 空矩阵 mat，随后模拟整个向内环绕的填入过程：

  * 定义当前左右上下边界 l,r,t,b，初始值 num = 1，迭代终止值 tar = n * n；
  * 当 num <= tar 时，始终按照 从左到右 从上到下 从右到左 从下到上 填入顺序循环，每次填入后：
    * 执行 num += 1：得到下一个需要填入的数字；
    * 更新边界：例如从左到右填完后，上边界 t += 1，相当于上边界向内缩 1。

  * 使用num <= tar而不是l < r || t < b作为迭代条件，是为了解决当n为奇数时，矩阵中心数字无法在迭代过程中被填充的问题。

* 最终返回 mat 即可。

<img src="https://pic.leetcode-cn.com/ccff416fa39887c938d36fec8e490e1861813d3bba7836eda941426f13420759-Picture1.png" alt="Picture1.png" style="zoom:48%;" />

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int l = 0, r = n - 1, t = 0, b = n - 1;
        int[][] mat = new int[n][n];
        int num = 1, tar = n * n;
        while(num <= tar){
            for(int i = l; i <= r; i++) mat[t][i] = num++; // left to right.
            t++;
            for(int i = t; i <= b; i++) mat[i][r] = num++; // top to bottom.
            r--;
            for(int i = r; i >= l; i--) mat[b][i] = num++; // right to left.
            b--;
            for(int i = b; i >= t; i--) mat[i][l] = num++; // bottom to top.
            l++;
        }
        return mat;
    }
}
```

## 实现一个程序，按顺序打印3个线程

```java
/**
 * 解法一
 */
public class PrintABCUseLock {

    private int times;
    private int state;

    private Lock lock = new ReentrantLock();

    public PrintABCUseLock(int times) {
        this.times = times;
    }

    void print(String str, int targetNum) {
        for (int i = 0; i < times;) {
            lock.lock();
            if (state % 3 == targetNum) {
                state++;
                i++;
                System.out.println(str);
            }
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        PrintABCUseLock foo = new PrintABCUseLock(3);
        new Thread(() -> {
            foo.print("A", 0);
        }).start();
        new Thread(() -> {
            foo.print("B", 1);
        }).start();
        new Thread(() -> {
            foo.print("C", 2);
        }).start();
    }
}

/**
 * 解法二
 */
public class PrintABCUseWait {
    private int times;
    private int state;
    private static final Object lock = new Object();

    public PrintABCUseWait(int times) {
        this.times = times;
    }

    void print(String str, int targetState) {
        for (int i = 0; i < times; i++) {
            synchronized (lock) {
                while (state % 3 != targetState) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                state++;
                System.out.println(str);
                lock.notifyAll();
            }
        }
    }

    public static void main(String[] args) {
        PrintABCUseWait demo = new PrintABCUseWait(3);
        new Thread(()-> {
            demo.print("A", 0);
        }).start();
        new Thread(()-> {
            demo.print("B", 1);
        }).start();
        new Thread(()-> {
            demo.print("C", 2);
        }).start();
    }
}
```

## 如何从很大的数中得到最大的k个数

1. 局部淘汰法：用一个容器保存前k个数，然后将后续的数字和容器内最小的数字进行比较，如果比容器内最小的数字大，替换掉容器内的数字，最后遍历完就得到最大的K个数。复杂度为O(N + K^2)
2. 分治法：将这批数分成100份，找出每份最大的K个数，最后在剩下的100*K个数据里面找出最大的K个
3. 最小堆法：取出前K个数，构建最小堆，然后用后续数字和顶点数字比较，如果比顶点的数字大，替换，重新构建最小堆。时间复杂度为O(N*KlogK)





# 分布式

## HTTP和RPC的区别

1. RPC可以基于http协议，也可以基于tcp协议。http只能基于http协议
2. rpc使用自定义的tcp协议，可以让请求报文体积更小，或者使用http2协议，也可以很好的减少报文的体积，提高传输效率。http如果基于http1协议，请求中会包含很多无用的内容
3. rpc可以基于thrift实现高效的二进制传输。http大部分基于json，比thrift耗时
4. rpc基本都自带了负载均衡策略。http需要配合Nginx来实现
5. rpc能做到自动通知，不影响上游。http需要事先通知，修改nginx配置

## raft算法是怎么实现的

raft将系统中的角色分为领导者（leader）、跟从者（follower）、候选人（candidate）

当服务器启动时，初始化为follower，leader周期性的向所有followers发送心跳，如果follower在选举超时时间内没有收到leader的心跳，就会在等待一段时间后触发一次leader选举。follower首先将自己的term加1转换为candidate，它首先给自己投票并且给集群中的其他节点发送RequestVote RPC，根据投票结果，选出新的leader

## raft心跳超时怎么办

会在等待自身竞选超时后发起选举

## 说一下分布式事务

分布式事务指事务的操作分布在不同的节点上，需要保证事务的ACID特性

解决方案有：

1. 两阶段提交（2PC）：通过协调者来协调参与者的行为，最终决定参与者是否真正执行事务

2. 补偿事务（TCC）：分为三个阶段try-confirm-cancel，try阶段对资源监测及预留，confirm确认操作，cancel做事务回滚
3. 本地消息表：事务操作的一方完成写操作后，将消息保存到本地消息表，之后通过定时任务等操作将本地消息表的内容发送到kafka等消息队列中，发送成功，删除该条消息，失败，继续重发。其他事务从kafka读取消息，执行相关操作
4. MQ消息事务：RocketMQ

## 说一下输入URL到显示的整个过程

1. 浏览器输入url，dns协议解析域名，找到对应的ip地址
2. 客户端通过tcp的三次握手和服务端简历tcp连接
3. 客户端向服务端发送http请求
4. 服务端返回一个http响应
5. 浏览器对资源进行加载解析，渲染页面



# 计算机网络 

## 说一下TCP

TCP是面向连接的，传输稳定可靠的、基于字节流的传输层传输协议，属于全双工可靠信道。

## TCP和UDP的区别

1. TCP是面向连接的，UDP无连接
2. TCP传送的数据可靠，不丢失。UDP不可靠
3. TCP面向字节流，UDP面向报文
4. TCP只能是点到点的，UDP支持一对一，一对多，多对一，多对多
5. TCP保证数据顺序，UDP不保证
6. TCP首部开销20字节，资源开销大。UDP首部8个字节，资源开销较小
7. TCP是全双工的可靠信道。UDP是不可靠信道

## TCP三次握手和四次挥手

 **三次握手**

<img src="https://img-blog.csdnimg.cn/20200714154451789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxMzU5OTUz,size_16,color_FFFFFF,t_70" alt="img" style="zoom:100%;" />

1. 第一次握手：Client状态位SYN置为1，随机产生一个序号seq=J，并将该数据表发送给Server，Clent进入SYN_SENT状态，等待Server确认
2. 第二次握手：Server收到数据包由SYN=1知道Client请求获取连接，Server把标志位SYN和ACK都置为1，ack=J+1，并随机产生一个序号seq=K，将数据包发给Client确认连接请求，Server进入SYN_RCVD状态
3. 第三次握手：Client收到Server的数据包，检查ack是否为J+1，ACK是否为1，如果正确，将标志位ACK置为1，ack=K+1，发送数据包给Server，Server检查ACK是否为1，ack是否为K+1，如果正确，建立连接成功，Client和Server进入ESTABLISHED状态。完成三次握手

**四次挥手**

![img](https://img-blog.csdnimg.cn/20200714154451881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxMzU5OTUz,size_16,color_FFFFFF,t_70)

1. 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client状态为FIN_WAIT_1
2. 第二次挥手：Server收到FIN后，发送一个ACK给Client，并将确认序号+1，Server进入CLOSE_WAIT状态，Client进入FIN_WAIT_2状态
3. 第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态
4. 第四次挥手：Client收到FIN后，进入TIME_WAIT状态，并且发送一个ACK给Client，确认序号为收到序号+1，Server进入CLOSED状态，四次挥手完毕

## 序列号和确认号的概念

序列号：seq序号，占32位，用来标记TCP源端向目的端发送的字节流，发起方发送数据时对此标记

确认号：ack序号，占32位，只有ACK标记为1时，ack才有效，为seq+1

## 为什么需要四次挥手

因为TCP是全双工的，因此，每个方向的数据流动都需要单独关闭。2次挥手关闭一个方向的数据流动，即不会收到数据了，但还是会发送数据，所以需要再进行2次挥手关闭这个方向的数据流动。

## 为什么要有TIME_WAIT状态

1. 确保有足够的时间让对方收到ACK包
2. 避免新旧连接混淆

## 产生大量CLOSE_WAIT的原因

1. 程序错误，没有正确释放连接
2. 网络问题，超时时间设置过短
3. 可能是apache等web服务器使用了keep-alive特性的副产物，正常现象

## 拥塞控制，拥塞窗口

拥塞控制就是用来防止过多数据注入到网络中，防止网络性能下降

拥塞控制的方法有：

1. 慢开始+拥塞避免

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005230105685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg4Mzg1NA==,size_16,color_FFFFFF,t_70#pic_center)

   * 开始拥塞窗口置为1，发送方只能发送一个TCP报文段，这时候使用的慢开始算法，指数增长，接收方回送一个TCP报文段，拥塞窗口置为2
   * 此时发送方可以发送2个报文段，接收方回送2个报文段的时候，发送方拥塞窗口置为4，发送方可以发送4个报文段。。。
   * 当拥塞窗口的值达到一个阈值，改为拥塞避免算法，拥塞窗口呈线性增长
   * 当拥塞窗口的值达到某个值，发生重传，没有收到确认报文段，这时拥塞窗口的值置为1，阈值置为当前拥塞窗口值的一半

2. 快重传+快恢复

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005232627169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg4Mzg1NA==,size_16,color_FFFFFF,t_70#pic_center)

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005233751649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg4Mzg1NA==,size_16,color_FFFFFF,t_70#pic_center)

   * 假如发送方在发送M3时报文段丢失了，当发送方发送M4时，接收方依旧发送确认M2的报文段给发送方，重复确认M2
   * 当重复确认M2三次时，发送方就知道M3丢失了，立刻重传M3
   * 接收方收到M3报文段时，发送确认M7报文段给发送方
   * 发生重传后，考虑到能够3次收到重复确认的报文段，代表网络不阻塞，将阈值置为当前拥塞窗口的一半，执行拥塞避免算法，这就是快恢复

## 滑动窗口

滑动窗口是一种流量控制机制

固定窗口大小发送方发送一次，必须等待响应，才能继续发送下一个请求，延迟很大

滑动窗口机制可以动态调解窗口大小，初始大小是根据链路带宽决定的，比如发送方发送一个序号为1，SIZE=3的数据，这时候发送方的窗口大小为3。接收方确认序号为3，这时候发送方收到确认序号后，就知道下次从序号为3的数据开始发送，这时候发送方窗口大小调整为2

## TCP协议如何保证可靠传输

1. **校验和**：发送的报文段的二进制数据相加后取反，接收方收到数据后计算进行比较，如果不一致，传输有误
2. **序列号与确认号**：接收方在收到数据包后返回确认号，发送方进行核对，如果有误，传输有误
3. **超时重传**：发送方在发送数据后等待一个时间，超时没收到，会重新发送
4. **连接管理**：三次握手，四次挥手
5. **流量控制**：滑动窗口
6. **拥塞控制**：慢开始+拥塞避免算法， 快开始+快恢复

## TCP重传机制

**快重传**

![这里写图片描述](https://img-blog.csdn.net/20180710115206319?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doZ3RoZW9uZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当接收端收到比期望序号值大的报文段时，会重复发送一次最近确认的报文段的确认信号，称为冗余ACK

比如发送方发送序号1的数据到接收方，接收方的期望序号为2，但是发送方序号2的报文丢失了，发送序号为3的报文，接收放收到序号为3的报文，与预期不一样，重复发送确认序号为2的报文，接收端收到连续的三次冗余ACK，发送方就知道是哪个报文段在传输中丢失了，于是重发该字段。

**超时重传**

TCP发出一个报文段时，启动一个定时器，超过这个时间还没收到这个确认，将重发这个报文段。

## 说说https

https基于http，加入了SSL协议，用于对http传输的数据进行加密，保证会话过程中的安全性。

## https中使用了哪些加密算法

对称加密和非对称加密混合

## https存在着哪些攻击的可能性，可以举个例子吗

BEAST、CRIME、TIME

攻击者控制受害者发送大量请求，利用压缩算法，猜测请求中的关键信息，根据response长度判断是否请求成功

控制GET请求地址，在GET地址处，不断变化字符串，进行猜测，猜测正确一位，返回的response就会减1

#  操作系统

## 线程和进程的区别

1. 进程是操作系统资源分配的基本单位。线程是任务调度和执行的基本单位。
2. 一个线程可以有多个线程
3. 同一进程的线程共享本进程的地址空间和资源。而进程之间的地址空间和资源是相互独立的

## 线程的状态

新建、就绪（可运行）、运行、阻塞、死亡

## 线程中阻塞和等待的区别

阻塞：被动阻塞，一个线程试图获取一个内部的对象锁，而该锁被其它线程持有，则该线程进入阻塞状态

等待：主动等待，一个线程等待另一个线程通知调度器一个条件时，该线程进入等待状态

## 操作系统访问内存的完整过程描述下

CPU发出一个虚拟地址给MMU，MMU通过页表，查询出这个虚拟地址对应的真实物理地址，访问内存条

## 阻塞IO和非阻塞IO的区别

**阻塞IO**

资源不可用时，IO请求一直阻塞，直到返回结果

**非阻塞IO**

资源不可用时，IO请求离开返回，返回数据标识资源不可用

## 如何实现一个带超时功能的connect函数

1. 首先将标志位设为Non-blocking模式，准备在非阻塞模式下调用connect函数

2. 调用connect，正常情况下，因为TCP三次握手需要一些时间；而非阻塞调用只要不能立即完成就会返回错误，所以这里会返回EINPROGRESS，表示在建立连接但还没有完成。

## 线程间的通信方式

锁机制、信号量机制、信号机制

## 进程之间的通信方式有哪些

管道、有名管道、信号量、消息队列、信号、共享内存、套接字

## 信号和信号量有什么区别

**信号**

信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

**信号量**

信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

## Linux中发送信号的命令是哪个

kill

## 说一下死锁的必要条件

1. 互斥：某种资源一次只允许一个进程访问
2. 占有且等待：一个进程本身占有资源，同时还有资源未得到满足，正在等待其他进程释放该资源
3. 不可抢占：别人已经占有了某项资源，你不能因为自己也需要改资源，就去把别人的资源抢过来
4. 循环等待：存在一个进程链，每个进程都占有下一个进程所需要的一种资源

## 说一下怎么解决死锁

1. 破坏占有且等待：所有的进程在开始运行之前，必须一次性申请所需要的全部资源
2. 破坏不可抢占：当一个已经持有了一些资源的进程提出新的资源请求没有得到满足时，它必须释放已经保持的所有资源，待以后需要的时候再重新申请
3. 破坏循环等待：将每个资源编号，当一个进程占有资源编号为i的资源时，下次申请资源只能申请编号大于i的资源

## 说一下进程的调度算法

1. 先来先服务
2. 时间片轮转法
3. 多级反馈队列算法
4. 最短进程优先
5. 最短剩余时间优先
6. 最高响应比优先