
# Android相关
---
## Activity和Fragment的生命周期
![](http://bmob-cdn-3365.b0.upaiyun.com/2016/07/15/a47f448b4077cefe80571168e3f03eef.png)

## 加速Activity启动
* 精简onCreate中的代码
* 将耗时操作放到后台线程
* 优化布局文件（ Hierarchy Viewer， Layoutopt）
* 缓存ListView

## Android多线程的几种方式
* Handler.sendXXXMessage()
* Handler.post(Runnable)
* Activity.runOnUIThread(Runnable)
* View.post(Runnable)
* AsyncTask

## 布局的优化
* HierarchyViewer查看Layout层次
* <include\> 标签重用一些比较复杂的组件
* <merge\> 标签减少层次,避免嵌套过深的情况发生
* 使用ViewStub减少隐藏View的绘制

## Android的几种缓存方法
* 内存缓存（使用LruCahe类，least recent used, 通过键值对的形式将对象储存在内存中，满了以后自动提出最不常用的对象）
* 磁盘缓存（使用DiskLurCache，数据库SQLite缓存，文件缓存）

## Android 屏幕适配
### *两个重要单位：*
* 密度无关像素(density-independent pixel, dp or dip):与终端上的实际物理像素点无关,可以保证在不同屏幕像素密度的设备上显示相同的效果。Android开发时用dp而不是px单位设置图片大小，是Android特有的单位。
* 独立比例像素(scale-independent pixel, sp or sip):Android开发时用此单位设置文字大小，可根据字体大小首选项进行缩放。
### *屏幕适配要解决的问题：*
* 使得“布局”、“控件”匹配不同的屏幕尺寸
* 使得“图片资源”匹配不同的屏幕密度
一个图片归纳解决办法,转自[这里](http://www.jianshu.com/p/ec5a1a30694b)：
![归纳解决办法](http://upload-images.jianshu.io/upload_images/944365-ced9745859537daf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 事件传递流程
TODO

## 自定义View
* 继承View，至少自定义两个构造函数
* 重写onMeasure方法：widthMeasureSpec中指定模式UNSPECIFIED，EXACTLY，AT_MOST，MATCH_PARENT对应EXACTLY、WRAP_CONTENT对应AT_MOST
* 重写onDraw：使用canvas和Paint类做图。
* 在res/values/styles.xml自定义属性

## Android OOM 处理

**原因：[参考](http://hanhailong.com/2015/12/27/Android%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E4%B9%8B%E5%B8%B8%E8%A7%81%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F/)**
* 单例造成的内存泄漏 (单例获取Activity的引用)
* 非静态内部类创建静态实例.(那为啥要用内部类？因为使用内部类能解决多继承问题)
* Handler造成的内存泄漏(静态+弱引用+removeCallbacksAndMessages)
* 线程造成的内存泄漏
* 图片的处理（压缩加缓存，压缩使用BufferedImage类）
* 资源未关闭造成的内存泄漏(BraodcastReceiver，ContentObserver，File，Cursor，Stream，Bitmap)

**发现：[参考](https://segmentfault.com/a/1190000006884310)**
* 反复操作观察内存变化
* 通过代码检测Activity泄漏
* 利用工具检测，如LeakCanary
* adb shell dumpsys meminfo
* 通过Capture分析.hprof文件

**处理：**
* 图片处理优化
* 使用保守的Service （IntentSevice执行完任务后自动关闭）
* 当视图变为隐藏状态后释放内存
* 内存资源紧张时释放内存(onTrimMemory()回调方法)
* 使用优化后的数据容器(SparseArray)
* 知道内存的开销(类500字节, 实例12到16字节,char 16bits　byte 8bits short 16bits int 32bits　long 64bits float 32bits double 64bits)

## 性能的优化
**卡顿原因：**
* 内存泄漏导致内存占用较高，导致JVM频繁触发GC
* UI线程做耗时任务
* UI OverDraw

## 进程/线程间通信：[参考1](https://my.oschina.net/u/248570/blog/53226) [ 参考2](http://www.jianshu.com/p/ac16f9702d68)
Linux上进程间通信: 套接口（Socket）, 管道（Pipe）,命名管道（named pipe）, 信号（Signal）, 信号量（semaphore）, 消息（Message）队列, 共享内存, 内存映射（mapped memory）
Android的进程间通信大多基于Bindler，参见下面Binder机制。
Android中线程间通信：共享变量（Sharepreferrence），使用管道流（Pipes），handle机制，runOnUiThread(Runnable)，View.postDelay(Runnable)
，广播

## Handler机制
Handler，Looper，MessageQueue，Message，Messenger

## Binder机制
TODO

## Volley机制（[参考这里](http://huachao1001.github.io/article.html?MfvbNAAH)）
通过一个RequestQueue维护所有网络请求。具体是：
1, RequestQueue的add函数接收到请求，判断其是否允许被缓存，如果不允许，则加入网络请求列队。
2, 如果允许缓存，则判断等待列队中是否有相同地址的请求，如果有，就加入等待列队，等待列队在每完成一次请求后访问，移除等待列队中相同的请求，并讲剩余的请求加入缓存列队。
3 如果没有相同的请求，则直接加入到缓存列队。缓存列队执行请求时先检查是否超时，如果超时则通过网络请求，如果未超时直接读取缓存。
![](http://bmob-cdn-3365.b0.upaiyun.com/2016/07/23/7cd35e7d40a290e48068bf7cc187e203.png)

## GreenDao机制

## Picasso机制
into会检查当前是否是在主线程上执行。
如果我们没有提供一个图片资源并且有设置placeholder，那么就会把我们设置的placeholder显示出来，并中断执行。
接下来就是创建了一个Request对象，我们在前面做得一些设置都会被封装到这个Request对象里面。
检查我们要显示的图片是否可以直接在缓存中获取，如果有就直接显示出来好了。
缓存没命中，那就只能费点事把源图片down下来了。这个过程是异步的，并且通过一个Action来完成请求前后的衔接工作。
![](http://www.trinea.cn/wp-content/uploads/2015/10/overall-design-picasso.jpg?x24892)

## 进程保活 ([参考这里](https://segmentfault.com/a/1190000006251859))

进程等级：
* 前台进程 (Foreground process)
* 可见进程 (Visible process)
* 服务进程 (Service process)
* 后台进程 (Background process)
* 空进程 (Empty process)

进程保活方法：
* 提供进程优先级，降低进程被杀死的概率
  * 利用 Activity 提升权限
  * 利用 Notification 提升权限(startForeground)
* 在进程被杀死后，进行拉活
  * 利用系统广播拉活
  * 利用第三方应用广播拉活
  * 利用系统Service机制拉活(onStartCommand()中返回Service.START_STICKY)
  * 利用Native进程拉活(利用 Linux 中的 fork 机制创建 Native 进程，在 Native 进程中监控主进程的存活，当主进程挂掉后，在 Native 进程中立即对主进程进行拉活)
  * 利用账号同步机制拉活

## 四种启动模式
**android:launchMode=:** 
standard(默认)、singleTop、singleTask、singleInstance

**FLAG:**
* **FLAG_ACTIVITY_NEW_TASK：** 与singleTask相同。
* **FLAG_ACTIVITY_SINGLE_TOP：** 与singleTop相同。
* **FLAG_ACTIVITY_CLEAR_TOP：** 清空目标Activity上层所有Activity。如果未设置launchMode（即默认），会清空包括当前Activity及其上面的activites，然后重新创建目标Activity。
* **FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS：** 具有这个标记的Activity不会出现在任务管理器的列表中，它等同于在XML中指定Activity的属性：android:excudeFromRecents="true"

## 三种动画
* 帧动画 FrameAnimation:通过顺序的播放排列好的图片来实现
* 补间动画 TweenAnimation: 给出两个关键帧，在给定的时间内在两个关键帧间渐变(Alpha、Scale、Translate和Rotate)。
* 属性动画 Property Animation:插值器根据时间流逝计算当前属性值改变百分比以不断改变属性值。

## Android长连接
* 客户端不断的查询服务器，检索新内容，也就是所谓的pull 或者轮询方式　
* 客户端和服务器之间维持一个TCP/IP长连接，服务器向客户端push　
* SMS的推送方式:服务器有新内容时，发送一条短信给客户端，客户端收到后从服务器中下载新内容

## MVC和MVP模式 ([参考](https://www.tianmaying.com/tutorial/AndroidMVC))
* 
## Android缓存
### 网络缓存（作业帮面试）([参考这里](https://www.2cto.com/kf/201502/376042.html))
---
# Java相关
---

## 1. Java并发，线程相关
### *什么是线程：*
* 线程是一种轻量级进程，大多数情况下用于执行异步操作。
* 在一个Android 程序开始运行的时候，会单独启动一个进程，同时会产生一个UIThread线程（main线程）。
* 一个Android 程序默认情况下也只有一个进程，但一个进程下却可以有许多个线程。
* 默认的情况下，Service和Activity（还有Content Provider和Broadcast Receiver）会同时运行在进程的main线程中，是会相互阻塞的。
### *线程和进程的区别：*
* 地址空间:进程有自己独立的地址空间.进程至少有一个线程,进程内的所有线程共享进程的地址空间;
* 资源拥有:进程是资源分配和拥有的单位,同一个进程内的线程共享进程的资源;
* 线程是处理器调度的基本单位,但进程不是.
* 线程只需要很少的资源就可“轻装上阵”运行的优点，来弥补进程并发的“颗粒度”粗糙的缺点，提高系统资源利用率。
### *volatile和Synchronized区别:*
* **volatile:**  它所修饰的变量不保留拷贝，直接访问主内存中的(保证可见性)。
* **synchronized:** 保证在同一时刻最多只有一个线程执行该段代码(保证原子性)。

### *ReetrankLock与synchronized比较*[参考](http://blog.csdn.net/ns_code/article/details/17487337)
* synchronized阻塞同步，挂起线程和恢复线程的操作消耗性能大，ReentrendLock非阻塞同步,消耗小
* ReentrendLock等待可中断，可实现公平锁，锁可以绑定多个条件。

### *原子性与可见性:*([参考这里](https://my.oschina.net/wangnian/blog/668490))
* **原子性：** 即一个操作或者多个操作 要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。
* **可见性:** 是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。

### *Callable 和 Runnable 的区别:*
* Runnable的run()没有返回值,Callable的call()有返回值,这个返回值具体是通过实现Future接口的对象的get方法获取的，这个方法是会造成线程阻塞的.
* Callable里面的call方法是可以抛出异常的，我们可以捕获异常进行处理

### *线程池的概念、好处、常见的线程池举例:*
* 线程池的好处：
  1. 重用线程池中的线程，避免因为线程的创建和销毁所带来的性能开销。
  2. 有效控制线程池的最大并发数，避免大量的线程之间因互相抢占系统资源而导致的阻塞线程。
  3. 能对线程进行简单的管理，并提供定时执行以及指定间隔循环执行等功能。
Android中线程池源自Java的Executor接口。真正的线程池实现为ThreadPoolExecutor。
ThreadPoolExecutor它的构造方法提供了一系列参数来配置线程池。
corePoolSize：线程池核心线程数量
maximumPoolSize：线程池所能容纳的最大线程。
keepAliveTime：非核心线程的闲置超长时间，超过这个时间，非核心线程就会被回收。
unit：用于指定keepAliveTime参数的时间单位。
workQueue：线程池中的任务队列
threadFactory：线程工厂，为线程池提供创建新线程的功能。

* 线程池的分类
  1. **FixedThreadPool**
Executors.newFixedThreadPool方法创建。他是一种固定线程数量的线程池，只有核心线程。这些线程不会被回收。除非线程池被关闭。
  2. **CachedThreadPool**
Executors.newCachedThreadPool方法创建,它一种线程数量不固定的线程池。它只有非核心线程。并且它的线程都有超时机制，超过60秒闲置线程就会被回收。
  3. **ScheduledThreadPool**
Executors.newScheduledThreadPool方法创建，它的核心数量是固定的，而非核心数量没有限制。当非核心线程闲置是会立刻回收。
  4. **SingleThreadExecutor**
Executors.newSingleThreadExecutor方法创建，它只要一个核心线程，能确保所有任务都在一个线程中按顺序执行。它的意义在于统一所有的外界任务到一个线程中。是这些线程任务之间不需要处理线程同步的问题。

### *请解释sleep()和wait()区别？*
* sleep()是Thread类的定义方法，wait()是Object类定义的方法;
* sleep()可以设置休眠时间，时间一到自动唤醒，而wait()需要等待notify()进行唤醒
* sleep()方法正在执行的线程主动让出CPU（然后CPU就可以去执行其他任务），在sleep指定时间后CPU再回到该线程继续往下执行(注意：sleep方法只让出了CPU，而并不会释放同步资源锁！！！)；
* wait()方法则是指当前线程让自己暂时退让出同步资源锁，以便其他正在等待该资源的线程得到该资源进而运行，只有调用了notify()方法，之前调用wait()的线程才会解除wait状态，可以去参与竞争同步资源锁，进而得到执行。（注意：notify的作用相当于叫醒睡着的人，而并不会给他分配任务，就是说notify只是让之前调用wait的线程有权利重新参与线程的调度

## 2. JVM内存空间分区（[参考这里](http://blog.csdn.net/a910626/article/details/52318590)）

- **程序计数器(Program Counter Register):** 程序计数器是当前线程所执行字节码的行号指示器
- **JVM栈(VM Stack):** JVM栈用于存放正在执行的方法，每有一个函数被调用，就会创建一个栈帧压入JVM栈，方法执行完成后，将其栈帧从JVM栈中弹出。所谓的栈帧，就是存放当前方法中的存储局部变量、操作数栈、动态链接、方法出口等信息的一个东西。
- **本地方法栈(Native Method Stack):** 本地方法栈与虚拟机栈所发挥的作用是类似的，其区别不过是本地方法栈则是为虚拟机使用到的native方法服务。
- **堆(Heap):** java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的是存放对象实例，几乎所有的对象实例都在这里分配内存。
- **方法区(Method Area):** 是各个线程共享的内存区域，它用于存放已经被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

## 3. GC算法（[参考这里](http://www.cnblogs.com/smyhvae/p/4744233.html)）
内存回收针对的是内存空间分区中的 **堆** 和 **方法区** 。
内存区域中的 **程序计数器、虚拟机栈、本地方法栈** 这3个区域随着线程而生，线程而灭，方法结束或者线程结束时，内存自然就跟着回收了，因此在这几个区域不需要过多考虑回收的问题。

* **引用计数算法：** 老牌垃圾回收算法。无法处理循环引用，没有被Java采纳.
* **根搜索算法:**  在根搜索算法的基础上，现代虚拟机的实现当中，垃圾搜集的算法主要有三种，分别是标记-清除算法、复制算法、标记-整理算法。这三种算法都扩充了根搜索算法.
* **标记-清除算法：**  通过根节点，标记所有从根节点开始的可达对象。在清除阶段，清除所有未被标记的对象。(缺点：1 效率比较低 2 清理出来的空闲内存不连续)
* **复制算法：** 从根节点，将可达对象复制到新的内存空间中，然后清除原来的内存空间。（清理后的空间是连续的，但是此方法需要较大的内存空间）
* **标记-压缩算法：** 标记步骤同标记-清除法，压缩是指移动所有存活的对象，且按照内存地址次序依次排列。最后将排列后末端内存地址以后的内存全部回收。（效率也不高）

## 4.  Java中的强引用、软引用、弱引用和虚引用

* **目的：** 对象的生命周期，以及利于 JVM 的垃圾回收
* **强引用：** 如果一个对象具有强引用，它就不会被垃圾回收器回收。
* **软引用：** 如果一个对象（如 s）只具有软引用，则内存空间足够，垃圾回收器就不会回收它；如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。
* **弱引用：** 弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。
* **虚引用：** 虚引用"顾名思义，就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。 虚引用主要用来跟踪对象被垃圾回收器回收的活动。

## 5. JAVA的多态(百度作业帮面试，融360面试)
### 什么是多态

* 面向对象的三大特性：封装、继承、多态。从一定角度来看，封装和继承几乎都是为多态而准备的。这是我们最后一个概念，也是最重要的知识点。
* 多态的定义：指允许不同类的对象对同一消息做出响应。即同一消息可以根据发送对象的不同而采用多种不同的行为方式。（发送消息就是函数调用）
* 实现多态的技术称为：动态绑定（dynamic binding），是指在执行期间判断所引用对象的实际类型，根据其实际的类型调用其相应的方法。
* 多态的作用：消除类型之间的耦合关系。

现实中，关于多态的例子不胜枚举。比方说按下 F1 键这个动作，如果当前在 Flash 界面下弹出的就是 AS 3 的帮助文档；如果当前在 Word 下弹出的就是 Word 帮助；在 Windows 下弹出的就是 Windows 帮助和支持。同一个事件发生在不同的对象上会产生不同的结果。

### 多态存在的三个必要条件

* 一、要有继承；
* 二、要有重写；
* 三、父类引用指向子类对象。

### 多态的好处：

* 1.可替换性（substitutability）。多态对已存在代码具有可替换性。例如，多态对圆Circle类工作，对其他任何圆形几何体，如圆环，也同样工作。
* 2.可扩充性（extensibility）。多态对代码具有可扩充性。增加新的子类不影响已存在类的多态性、继承性，以及其他特性的运行和操作。实际上新加子类更容易获得多态功能。例如，在实现了圆锥、半圆锥以及半球体的多态基础上，很容易增添球体类的多态性。
* 3.接口性（interface-ability）。多态是超类通过方法签名，向子类提供了一个共同接口，由子类来完善或者覆盖它而实现的。如图8.3 所示。图中超类Shape规定了两个实现多态的接口方法，computeArea()以及computeVolume()。子类，如Circle和Sphere为了实现多态，完善或者覆盖这两个接口方法。
* 4.灵活性（flexibility）。它在应用中体现了灵活多样的操作，提高了使用效率。
* 5.简化性（simplicity）。多态简化对应用软件的代码编写和修改过程，尤其在处理大量对象的运算和操作时，这个特点尤为突出和重要。

### Java中多态的实现方式：
**接口实现，继承父类进行方法重写，同一个类中进行方法重载。**

## 6.static

### 静态内部类与非静态内部类区别

* 静态内部类不需要有指向外部类的引用，但非静态内部类需要持有对外部类的引用
* 非静态内部类能够访问外部类的静态和非静态成员，静态类不能访问外部类的非静态成员，它只能访问外部类的静态成员
* 一个非静态内部类不能脱离外部类实体被创建,而静态内部类不需要外部类的对象就可创建。

## 7.Java中的修饰符
### 访问修饰符（[参考](http://henry-cong.iteye.com/blog/1140277)）

**public：** Java语言中访问限制最宽的修饰符，一般称之为“公共的”。被其修饰的类、属性以及方法不
　　　　　仅可以跨类访问，而且允许跨包（package）访问。
**private:** Java语言中对访问权限限制的最窄的修饰符，一般称之为“私有的”。被其修饰的类、属性以
　　　　　及方法只能被该类的对象访问，其子类不能访问，更不能允许跨包访问。
**protect:** 介于public 和 private 之间的一种访问修饰符，一般称之为“保护形”。被其修饰的类、
　　　　　属性以及方法只能被类本身的方法及子类访问，即使子类在不同的包中也可以访问。
**default:** 即不加任何访问修饰符，通常称为“默认访问模式“。该模式下，只允许在同一个包中进行访
　　　　　问。
     
## 8.类的加载机制 ([参考](http://www.jianshu.com/p/061274672c44))

**双亲委派模型：** 如果一个类加载器收到类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器完成。每个类加载器都是如此，只有当父加载器在自己的搜索范围内找不到指定的类时（即ClassNotFoundException），子加载器才会尝试自己去加载。

**自上而下的加载器顺序：** Bootstrap ClassLoader(启动类加载器)，Extension ClassLoader(扩展类加载器)，Applicaiton ClassLoader(应用程序类加载器)，User ClassLoader（用户自己实现的加载器） 
![](http://upload-images.jianshu.io/upload_images/689802-bc4898e54c6ee6fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 9. Java的注解原理
// TODO
---
# 网络相关
---

## http协议([参考这里](http://www.blogjava.net/zjusuyong/articles/304788.html))
### 什么是http协议
http协议是超文本传输协议，是一种基于请求与相应模式的、无状态的、应用层协议，常基于TCP的连接方式。
### http协议特点
http协议主要有以下五个特点：

 - 支持客户/服务器模式
 - 简单快速：客户想服务器请求服务时，只需要传递方法和路径，请求方法常用的有GET、HEAD、POST。由于HTTP协议简单，使得HTTP服务器程序规模小，因而通信速度快。
 - 灵活：HTTP允许传输任意类型数据对象。正在传输的类型由Content-Type加以标记。
 - 无连接：无连接含义是限制在每次连接只处理一个请求。服务器处理完客户请求，并收到客户应答后，即断开连接。
 - 无状态：HTTP协议是无状态协议。协议对于事务没有记忆能力，缺少状态以为着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大，另一方面，在服务器不需要先前信息时应答会较快。
### http请求消息
请求消息包括：请求行、消息报头、请求正文组成。

![](https://i.imgur.com/TZGiZid.jpg)

**请求方法**包括：

 - GET     请求获取Request-URI所标识的资源
 - POST    在Request-URI所标识的资源后附加新的数据
 - HEAD    请求获取由Request-URI所标识的资源的响应消息报头
 - PUT     请求服务器存储一个资源，并用Request-URI作为其标识
 - DELETE  请求服务器删除Request-URI所标识的资源
 - TRACE   请求服务器回送收到的请求信息，主要用于测试或诊断
 - CONNECT 保留将来使用
 - OPTIONS 请求查询服务器的性能，或者查询与资源相关的选项和需求
### http响应消息
HTTP响应也是由三个部分组成，分别是：状态行、消息报头、响应正文。

![](https://i.imgur.com/TA4ZTPa.png)

状态行：状态行由 HTTP 协议版本字段、状态码和状态码的描述文本 3 个部分组成，他们之间使用空格隔开;

### http请求/相应步骤

**以下是 HTTP 请求/响应的步骤：**

　　● 客户端连接到web服务器：HTTP 客户端与web服务器建立一个 TCP 连接;

　　● 客户端向服务器发起 HTTP 请求：通过已建立的TCP 连接，客户端向服务器发送一个请求报文;

　　● 服务器接收 HTTP 请求并返回 HTTP 响应：服务器解析请求，定位请求资源，服务器将资源副本写到 TCP 连接，由客户端读取;

　　● 释放 TCP 连接：若connection 模式为close，则服务器主动关闭TCP 连接，客户端被动关闭连接，释放TCP 连接;若connection 模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求;

　　● 客户端浏览器解析HTML内容：客户端将服务器响应的 html 文本解析并显示;

**在浏览器地址栏键入URL，按下回车之后会经历以下流程：**

　　1、浏览器向 DNS 服务器请求解析该 URL 中的域名所对应的 IP 地址;

　　2、解析出 IP 地址后，根据该 IP 地址和默认端口 80，和服务器建立 TCP 连接;

　　3、浏览器发出读取文件(URL 中域名后面部分对应的文件)的HTTP 请求，该请求报文作为 TCP 三次握手的第三个报文的数据发送给服务器;

　　4、服务器对浏览器请求作出响应，并把对应的 html 文本发送给浏览器;

　　5、释放 TCP 连接;

　　6、浏览器将该 html 文本并显示内容;

### http协议状态码

状态码由三位数字组成，第一位数字表示响应的类型，常用的状态码有五大类如下所示：

　　1xx：表示服务器已接收了客户端请求，客户端可继续发送请求;

　　2xx：表示服务器已成功接收到请求并进行处理;

　　3xx：表示服务器要求客户端重定向;

　　4xx：表示客户端的请求有非法内容;

　　5xx：表示服务器未能正常处理客户端的请求而出现意外错误;

常见状态码描述文本有如下取值：

　　200 OK：表示客户端请求成功;

　　400 Bad Request：表示客户端请求有语法错误，不能被服务器所理解;

　　401 Unauthonzed：表示请求未经授权，该状态代码必须与 WWW-Authenticate 报头域一起使用;

　　403 Forbidden：表示服务器收到请求，但是拒绝提供服务，通常会在响应正文中给出不提供服务的原因;

　　404 Not Found：请求的资源不存在，例如，输入了错误的URL;

　　500 Internal Server Error：表示服务器发生不可预期的错误，导致无法完成客户端的请求;

　　503 Service Unavailable：表示服务器当前不能够处理客户端的请求，在一段时间之后，服务器可能会恢复正常;

### Get与Post区别

**1.提交**

 - GET提交，请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，多个参数用&连接；
 - POST提交：把提交的数据放置在是HTTP包的包体中。

>因此，GET提交的数据会在地址栏中显示出来，而POST提交，地址栏不会改变



**2.传输数据的大小**：首先声明：HTTP协议没有对传输的数据大小进行限制，HTTP协议规范也没有对URL长度进行限制。

而在实际开发中存在的限制主要有：

 - GET:特定浏览器和服务器对URL长度有限制，例如 IE对URL长度的限制是2083字节(2K+35)。对于其他浏览器，如Netscape、FireFox等，理论上没有长度限制，其限制取决于操作系 统的支持。

>因此对于GET提交时，传输数据就会受到URL长度的 限制。

 - POST:由于不是通过URL传值，理论上数据不受 限。但实际各个WEB服务器会规定对post提交数据大小进行限制，Apache、IIS6都有各自的配置。

**3.安全性**

　　POST的安全性要比GET的安全性高。比如：通过GET提交数据，用户名和密码将明文出现在URL上，因为:

 - 登录页面有可能被浏览器缓存；
 - 其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了.
 - 使用GET提交数据还可能会造成Cross-site request forgery攻击

**4.Http get,post,soap协议都是在http上运行的**

 - get：请求参数是作为一个key/value对的序列（查询字符串）附加到URL上的
查询字符串的长度受到web浏览器和web服务器的限制（如IE最多支持2048个字符），不适合传输大型数据集同时，它很不安全

 - post：请求参数是在http标题的一个不同部分（名为entity body）传输的，这一部分用来传输表单信息，因此必须将Content-type设置为:application/x-www-form- urlencoded。post设计用来支持web窗体上的用户字段，其参数也是作为key/value对传输。
但是：它不支持复杂数据类型，因为post没有定义传输数据结构的语义和规则。

 - soap：是http post的一个专用版本，遵循一种特殊的xml消息格式
Content-type设置为: text/xml 任何数据都可以xml化。

###http协议与https协议区别
 - https协议需要到ca申请证书，一般免费证书很少，需要交费。 
 - http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。 
 - http和https使用的是完全不同的连接方式用的端口也不一样,前者是80,后者是443。 
 - http的连接很简单,是无状态的。 
 - HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议 要比http协议安全。

## TCP协议
TCP协议报头：

![](http://upload-images.jianshu.io/upload_images/2964446-ab077ff3902529a3.jpg?imageMogr2/auto-orient/strip)

###URG ACK PSH RST SYN FIN字段作用
 - URG：紧急标志。紧急标志为"1"表明该位有效。

 - ACK：确认标志。表明确认编号栏有效。大多数情况下该标志位是置位的。TCP报头内的确认编号栏内包含的确认编号（w+1）为下一个预期的序列编号，同时提示远端系统已经成功接收所有数据。

 - PSH：推标志。该标志置位时，接收端不将该数据进行队列处理，而是尽可能快地将数据转由应用处理。在处理Telnet或rlogin等交互模式的连接时，该标志总是置位的。

 - RST：复位标志。用于复位相应的TCP连接。

 - SYN：同步标志。表明同步序列编号栏有效。该标志仅在三次握手建立TCP连接时有效。它提示TCP连接的服务端检查序列编号，该序列编号为TCP连接初始端（一般是客户端）的初始序列编号。在这里，可以把TCP序列编号看作是一个范围从0到4，294，967，295的32位计数器。通过TCP连接交换的数据中每一个字节都经过序列编号。在TCP报头中的序列编号栏包括了TCP分段中第一个字节的序列编号。

 - FIN：结束标志。
### TCP三次握手连接
![](http://upload-images.jianshu.io/upload_images/2964446-aa923712d5218eeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 - 第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。

 - 第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，ack=J+1，随机产生一个值seq=K，并将该数据包发送给Client以确认连接请求，Server进入SYN_RCVD状态。

 - 第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否为1，如果正确则连接建立成功，Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据了。

简单来说，就是

1. 建立连接时，客户端发送SYN包（SYN=i）到服务器，并进入到SYN-SEND状态，等待服务器确认
2. 服务器收到SYN包，必须确认客户的SYN（ack=i+1）,同时自己也发送一个SYN包（SYN=k）,即SYN+ACK包，此时服务器进入SYN-RECV状态
3. 客户端收到服务器的SYN+ACK包，向服务器发送确认报ACK（ack=k+1）,此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手，客户端与服务器开始传送数据。
###TCP四次挥手断开连接
![](http://upload-images.jianshu.io/upload_images/2964446-2b9562b3a8b72fb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　由于TCP连接时全双工的，因此，每个方向都必须要单独进行关闭，这一原则是当一方完成数据发送任务后，发送一个FIN来终止这一方向的连接，收到一个FIN只是意味着这一方向上没有数据流动了，即不会再收到数据了，但是在这个TCP连接上仍然能够发送数据，直到这一方向也发送了FIN。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上图描述的即是如此。

 - 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。

 - 第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。

 - 第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。

 - 第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。
### Tcp为什么要三次握手，四次挥手
一段通俗的解释

三次握手：

A:“喂，你听得到吗？”A->SYN_SEND

B:“我听得到呀，你听得到我吗？”应答与请求同时发出 B->SYN_RCVD | A->ESTABLISHED

A:“我能听到你，今天balabala……”B->ESTABLISHED

四次挥手：

A:“喂，我不说了。”A->FIN_WAIT1

B:“我知道了。等下，上一句还没说完。Balabala…..”B->CLOSE_WAIT | A->FIN_WAIT2（此处可能没有就是恰好B也发送完了，并且此处不在四次握手内，是正常通信的消息）

B:”好了，说完了，我也不说了。”B->LAST_ACK

A:”我知道了。”A->TIME_WAIT | B->CLOSED

A等待2MSL,保证B收到了消息,否则重说一次”我知道了”,A->CLOSED

### TCP与UDP区别

1. 基于连接与无连接；
2. 对系统资源的要求（TCP较多，UDP少）；
3. UDP程序结构较简单；
4. TCP流模式与UDP数据报模式 ；
5. TCP保证数据正确性，UDP可能丢包，TCP保证数据顺序，UDP不保证。
## TCP、UDP的区别
TCP是面向连接的，可靠的，传输大量数据，速度慢。UDP相反。
## TCP的三次握手、四次挥手
* 三次握手： 为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误
* 四次挥手：TCP是全双工模式，这就意味着，
  * 当主机1发出FIN报文段时，告诉主机2，它的数据已经全部发送完毕了,但是，这个时候主机1还是可以接受来自主机2的数据；
  * 当主机2返回ACK报文段时，表示它已经知道主机1没有数据发送了，但是主机2还是可以发送数据到主机1的；
  * 当主机2也发送了FIN报文段时，这个时候就表示主机2也没有数据要发送了;
  * 当主机1返回ACK报文段之后彼此就会愉快的中断这次TCP连接。
## HTTP的特点
* 支持客户/服务器模式。
* 简单快速：客户向服务器请求服务时，只需传送请求方法(GET、HEAD、POST)和路径。
由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
* 灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。
* 无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
* 无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。
## HTTP与HTTPS的区别

---
# 算法相关（[参考这里](https://getpocket.com/a/read/982518844)）
---
* **二位数组查找：** 对数组进行排序，没一行从左到右递增，每一列从上往下递增。然后从右上角开始，比较与target的大小，如果等于，结束查找；如果大于target，则删除此列，如果小于target，删除此行；然后从剩下的数组右上角开始查找。
* **替换字符串中空格（替换数组中的元素x）：** 先遍历一遍，统计字符串A中空格个数，然后计算替换后的总长度，生成新的长度的空字符串B。一个指针指向B的结尾，一个指向A的结尾，从后往前遍历A，遇到空格就在B中插入要替换的字符。
* **从尾到头打印链表（栈的使用）：** 利用栈的先进先出原则
* **二进制中1的个数：** 如果一个整数不为0，那么这个整数至少有一位是1。如果我们把这个整数减1，那么原来处在整数最右边的1就会变为0，原来在1后面的所有的0都会变成1(如果最右边的1后面还有0的话)。其余所有位将不会受到影响。
* **数值的整数次方：** a^n=a^(n/2)*a^(n/2)，(n为偶数)；或者 a^n = a^ ((n-1)/2)*a^((n-1)/2)*a，(n为奇数)

---
# 数据库相关

----------

## *三范式*
即：属性唯一，记录唯一和表唯一

* 第一范式（1NF）：数据库表中的字段都是单一属性，不可再分。这个单一属性由基本类型构成，包括整数、实数、字符型、逻辑型、日期型等。
* 第二范式（2NF）：数据库表中不存在非关键字段对任意候选关键字段的部分函数依赖（部分函数依赖指的是存在组合关键字中某些字段决定非关键字段的情况），也即所有非关键字段都完全依赖任意组候选关键字。
* 第三范式（3NF）：在第二范式基础上，数据表中如果不存在非关键字段对任一候选字段的传递函数依赖则符合第三范式。所谓传递函数依赖，指的是如 果存在"A → B → C"的决定关系，则C传递函数依赖于A。因此，满足第三范式的数据库表应该不存在如下依赖关系： 关键字段 → 非关键字段 x → 非关键字段y

## *数据库基本语法*

### HAVING和WHERE对于分组查询的区别
HAVING子句对GROUP BY子句设置条件的方式与WHERE子句和SELECT语句交互的方式类似。WHERE子句搜索条件在进行分组操作之前应用；而HAVING搜索条件在进行分组操作之后应用。HAVING语法与WHERE语法类似，但HAVING可以包含聚合函数。HAVING子句可以引用选择列表中出现的任意项。

### Full outer join，left join, right join，inner join区别([参考这里](http://www.cnblogs.com/logon/p/3748020.html))

#### 外连接

1.概念：包括左向外联接、右向外联接或完整外部联接

2.左连接：left join 或 left outer join，
左向外联接的结果集包括 LEFT OUTER 子句中指定的左表的所有行，而不仅仅是联接列所匹配的行。如果左表的某行在右表中没有匹配行，则在相关联的结果集行中右表的所有选择列表列均为空值(null)。

3.右连接：right join 或 right outer join。
右向外联接是左向外联接的反向联接。将返回右表的所有行。如果右表的某行在左表中没有匹配行，则将为左表返回空值。

4.完整外部联接:full join 或 full outer join，完整外部联接返回左表和右表中的所有行。当某行在另一个表中没有匹配行时，则另一个表的选择列表列包含空值。如果表之间有匹配行，则整个结果集行包含基表的数据值。
#### 内连接
1.概念：内联接是用比较运算符比较要联接列的值的联接

2.内连接：join 或 inner join，只返回符合条件的列。

#### 交叉连接
1.概念：没有 WHERE 子句的交叉联接将产生联接所涉及的表的笛卡尔积。第一个表的行数乘以第二个表的行数等于笛卡尔积结果集的大小。

2.交叉连接：cross join 
### 查询([更多语句参考此处](http://blog.csdn.net/zhanghaotian2011/article/details/8904365))

* 查询数据库表最后一条记录(ID自增)
```
SELECT top 1 * FROM Table_Name ORDERBY ID DESC
```
```
SELECT * FROM Table_Name WHERE ID=(SELECT MAX(ID) FROM Table_Name)
```
* 查询数据库表A中31-40条记录
```
SELECT top 10 * FROM A WHERE ID not in(SELECT top 30 ID FROM A)
```
* 查询员工平均工资大于3000的部门名称
```
SELECT Dept_Name FROM t_Dept WHERE ID in(SELECT Dept_ID FROM t_Salary GROUP BY Dept_ID HAVING avg(Salary)>3000)
```

-------
# 数据库相关
-------
## B树（百度作业帮面试）
### 1.B树
　　Ｂ树及Ｂ－树，是一种自平衡的树，能够**保持数据有序**。这种数据结构能够让查找数据、顺序访问、插入数据及删除的动作，都在对数时间内完成。

　　B树，概括来说是一个一般化的**二叉查找树**（binary search tree），可以拥有**多于2个子节点**。与自平衡二叉查找树不同，B树为系统大块数据的读写操作做了优化。B树减少定位记录时所经历的中间过程，从而加快存取速度。B树这种数据结构可以用来描述外部存储。这种数据结构常被应用在数据库和文件系统的实作上。

#### 1.1　B-树的性质

M为树的阶数，B-树或为空树，否则满足下列条件：

1.　定义任意非叶子结点最多只有M个儿子；且M>2；

2.　根结点的儿子数为[2, M]；

3.　除根结点以外的非叶子结点的儿子数为[M/2, M]；

4.　每个结点存放至少M/2-1（取上整）和至多M-1个关键字；（至少2个关键字）

5.　非叶子结点的关键字个数=指向儿子的指针个数-1；

6.　非叶子结点的关键字：K[1], K[2], …, K[M-1]；且K[i] < K[i+1]；

7.　非叶子结点的指针：P[1], P[2], …, P[M]；其中P[1]指向关键字小于K[1]的子树，P[M]指向关键字大于K[M-1]的子树，其它P[i]指向关键字属于(K[i-1], K[i])的子树；

8.　所有叶子结点位于同一层；


如：（M=3）
![这里写图片描述](http://img.blog.csdn.net/20160612115550177)

　　B-树的搜索，从根结点开始，对结点内的关键字（有序）序列进行二分查找，如果命中则结束，否则进入查询关键字所属范围的儿子结点；重复，直到所对应的儿子指针为空，或已经是叶子结点。


#### 1.２　B树的运用场景

 - 保持**键值有序**，以顺序遍历

 - 使用**层次化的索引来最小化磁盘读取**

 - 使用**不完全填充**的块来加速插入和删除

 - 通过优雅的遍历算法来保持**索引平衡**
 
 另外，B树通过保证内部节点至少半满来**最小化空间浪费**。一棵B树可以处理任意数目的插入和删除。


### ２.　B＋树

　　B+ 树是一种树数据结构，是一个**n叉树**，每个节点通常有多个孩子，一颗B+树包含**根节点、内部节点和叶子节点**。根节点可能是一个叶子节点，也可能是一个包含两个或两个以上孩子节点的节点。

　　B+ 树通常用于**数据库和操作系统的文件系统**中。NTFS, ReiserFS, NSS, XFS, JFS, ReFS 和BFS等文件系统都在使用B+树作为**元数据索引**。B+ 树的特点是能够保持**数据稳定有序**，其插入与修改拥有较稳定的对数时间复杂度。B+ 树元素自底向上插入。

#### ２.1　B＋树的性质

B+树是B-树的变体，也是一种多路搜索树，其定义基本与B-树同，除了：

１.　非叶子结点的子树指针与关键字个数相同；

２.　非叶子结点的子树指针P[i]，指向关键字值属于[K[i], K[i+1])的子树（B-树是开区间）；

３.　为所有叶子结点增加一个链指针；

４.　所有关键字都在叶子结点出现；

![这里写图片描述](http://img.blog.csdn.net/20160612134221890)　

#### ２.２　B＋树与Ｂ树的区别

1.　所有关键字都出现在叶子结点的链表中（稠密索引），且链表中的关键字恰好是有序的；

2.　不可能在非叶子结点命中；

3.　非叶子结点相当于是叶子结点的索引（稀疏索引），叶子结点相当于是存储（关键字）数据的数据层；

4.　**更适合文件索引系统**；

　　 B+的搜索与B-树也基本相同，区别是B+树只有达到叶子结点才命中（B-树可以在非叶子结点命中），其性能也等价于在关键字全集做一次二分查找；

### ３.　B*树

B+树的变体，在B+树的非根和非叶子结点再增加指向兄弟的指针；

![这里写图片描述](http://img.blog.csdn.net/20160612134707432)　

　　 B*树定义了非叶子结点关键字个数至少为(2/3)*M，即块的最低使用率为2/3（代替B+树的1/2）；

B+树的分裂：
　　
> 当一个结点满时，分配一个新的结点，并将原结点中1/2的数据复制到新结点，最后在父结点中增加新结点的指针；B+树的分裂只影响原结点和父结点，而不会影响兄弟结点，所以它不需要指向兄弟的指针；

B*树的分裂：

>当一个结点满时，如果它的下一个兄弟结点未满，那么将一部分数据移到兄弟结点中，再在原结点插入关键字，最后修改父结点中兄弟结点的关键字（因为兄弟结点的关键字范围改变了）；如果兄弟也满了，则在原结点与兄弟结点之间增加新结点，并各复制1/3的数据到新结点，最后在父结点增加新结点的指针；

所以，B*树分配新结点的概率比B+树要低，**空间使用率更高**；

### 4 总结

 - B-树：
 　　
 　　多路搜索树，每个结点存储M/2到M个关键字，非叶子结点存储指向关键字范围的子结点；所有关键字在整颗树中出现，且只出现一次，非叶子结点可以命中；

 - B+树：

　　在B-树基础上，为叶子结点增加链表指针，所有关键字都在叶子结点
中出现，非叶子结点作为叶子结点的索引；B+树总是到叶子结点才命中；

 - B*树：

　　在B+树基础上，为非叶子结点也增加链表指针，将结点的最低利用率从1/2提高到2/3；

# 其他 
1. ## 了解哪些加密算法 ([参考](http://www.cnblogs.com/MikeChen/archive/2011/04/22/2024574.html))
* 对称加密 ： DES、3DES、Blowfish、IDEA、RC4、RC5、RC6和AES （归纳 DES, RC*, AES）
* 非对称加密: RSA、ECC（移动设备用）、Diffie-Hellman、El Gamal、DSA
* Hash算法加密（不可逆向解密）：MD2、MD4、MD5、HAVAL、SHA
