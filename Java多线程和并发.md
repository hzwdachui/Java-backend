# Java 多线程面试

## 什么是线程
- 操作系统调度最小单元，进程中实际运作单位
## 为什么使用线程（优点）
- 多个线程并发执行提高程序的效率
- CPU不会因为需要等待某个线程而空闲
- 线程分享内存
## 进程和线程之间有什么不同
- 进程是在系统内从中加载运行的程序，进程是一个进程内实际运作单元（线程属于进程）
- 进程内存独立，线程共享内存（线程仍然拥有自己的栈和局部变量）

## 如何创建线程，怎么创建更好
- 继承thread类，重写run()方法，创建thread实例，再run()
- 实现Runnable 接口，并重写run()方法

(因为thread类本身就是实现的Runnable接口)  
用哪一个更好？

- Java不支持类的多重继承但是支持调用多个接口
- 所以如果有其他类需要继承，则调用Runnable接口更好

## Thread 类中run() start()区别
- start()方法被用来启动新创建的线程，而且start()内部调用了run()方法，这和直接调用run()方法的效果不一样。当你调用run()方法的时候，只会是在原来的线程中调用，没有新的线程启动，start()方法才会启动新线程
- 反正就是要用start

## Runnable 和 Callable接口的区别
- 都代表在不同线程中执行的任务
- 区别是Callable的call()方法可以1）返回值和2）抛出异常值(check exception)

## Java内存模型是什么？（没懂）
**Java内存模型规定和指引Java程序在不同的内存架构、CPU和操作系统间有确定性地行为。它在多线程的情况下尤其重要。**Java内存模型对一个线程所做的变动能被其它线程可见提供了保证，它们之间是先行发生关系。这个关系定义了一些规则让程序员在并发编程时思路更清晰。比如，先行发生关系确保了：

线程内的代码能够按先后顺序执行，这被称为程序次序规则。
对于同一个锁，一个解锁操作一定要发生在时间上后发生的另一个锁定操作之前，也叫做管程锁定规则。
前一个对volatile的写操作在后一个volatile的读操作之前，也叫volatile变量规则。
一个线程内的任何操作必需在这个线程的start()调用之后，也叫作线程启动规则。
一个线程的所有操作都会在线程终止之前，线程终止规则。
一个对象的终结操作必需在这个对象构造完成之后，也叫对象终结规则。
可传递性

## 对比CountDownLatch, CyclicBarrier, Semophore
- CountDownLatch： 一个thread等待其他的thread。首先创建latch，会开始一个counter，然后用``await()``设定等待的thread，等待其他thread完成，其他thread需要使用``latch.countDown``。
- CyclicBarrier: 通过它可以实现让**一组线程等待至某个状态之后再全部同时执行**。叫做回环是因为当所有等待线程都被释放以后，**CyclicBarrier可以被重用**。我们暂且把这个状态就叫做barrier，当调用await()方法之后，线程就处于barrier了。
- Semaphore可以控同时访问的线程个数，通过 acquire() 获取一个许可，如果没有就等待，而 release() 释放一个许可。（limited pool of resources）
## Java中的volatile 变量是什么？
- 一个修饰符，只有成员变量能用，线程都会直接读取该变量并且不缓存它
- 保证下一个读取在前一个写之后发生（？）
- 线程读取到的变量是同内存中一致的

## 什么是线程安全？Vector是一个线程安全类吗？
如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。（说白了就是每次结果都跑的一样）

一个线程安全的计数器类的同一个实例对象在被多个线程使用的情况下也不会出现计算失误。很显然你可以将集合类分成两组，线程安全和非线程安全的。**Vector 是用同步方法来实现线程安全的, 而和它相似的ArrayList不是线程安全的。**

## 竞态条件
竞态条件会导致程序在并发情况下出现一些bugs。多线程对一些资源的竞争的时候就会产生竞态条件，如果首先要执行的程序竞争失败排到后面执行了，那么整个程序就会出现一些不确定的bugs。这种bugs很难发现而且会重复出现，因为线程间的随机竞争。

## 停止/暂停线程
- 执行完会自动结束
- 用volatile 布尔变量来退出run()方法的循环或者是取消任务来中断线程（？）
- Thread类的Sleep()方法让线程暂停一段时间（不是终止）

## Java多线程中调用wait() 和 sleep()方法有什么不同？
Java程序中wait 和 sleep都会造成某种形式的暂停，它们可以满足不同的需要。wait()方法用于线程间通信，如果等待条件为真且其它线程被唤醒时它会释放锁，而sleep()方法仅仅释放CPU资源或者让当前线程停止执行一段时间，但不会释放锁。你可以查看这篇文章获得更多信息。

## 一个线程运行时发生异常会怎样？（?）
停止执行  
uncaughtException方法会处理

## 如何在两个线程间共享数据（the status of resource）？
- 你可以通过**共享对象**来实现这个目的
- 或者是使用像**阻塞队列**这样并发的数据结构。（**Object类中wait()\notify()\notifyAll()方法**可以用于线程间通信关于资源的锁的状态）（？）(生产者消费者模型?)

wait, notify, notifyAll

- wait
Object wait methods has three variance, one which waits indefinitely for any other thread to call notify or notifyAll method on the object to wake up the current thread. Other two variances puts the current thread in wait for specific amount of time before they wake up.

- notify
notify method wakes up only one thread waiting on the object and that thread starts execution. So if there are multiple threads waiting for an object, this method will wake up only one of them. The choice of the thread to wake depends on the OS implementation of thread management.

- notifyAll
notifyAll method wakes up all the threads waiting on the object, although which one will process first depends on the OS implementation.

## 为什么wait, notify 和 notifyAll这些方法不在thread类里面？
**一句话：锁是对象的，这样每个类都有线程间通讯的基本方法。**

这是个设计相关的问题，它考察的是面试者对现有系统和一些普遍存在但看起来不合理的事物的看法。回答这些问题的时候，你要说明为什么把这些方法放在Object类里是有意义的，还有不把它放在Thread类里的原因。

一个很明显的原因是JAVA提供的**锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得**。如果线程需要等待某些锁那么调用对象中的wait()方法就有意义了。如果wait()方法定义在Thread类中，线程正在等待的是哪个锁就不明显了。

简单的说，由于wait，notify和notifyAll都是锁级别的操作，所以把他们定义在Object类中因为锁属于对象。

## 14. 为什么wait(), notify()和notifyAll()必须在同步方法或者同步块中被调用？
当一个线程需要调用对象的wait()方法的时候，这个线程必须拥有该对象的锁，接着它就会释放这个对象锁并进入等待状态直到其他线程调用这个对象上的notify()方法。

同样的，当一个线程需要调用对象的notify()方法时，它会释放这个对象的锁，以便其他在等待的线程就可以得到这个对象锁。

**由于所有的这些方法都需要线程持有对象的锁，这样就只能通过同步来实现，所以他们只能在同步方法或者同步块中被调用。**

## 什么是ThreadLocal变量？
用来消除线程间的竞争

ThreadLocal用于创建线程的本地变量，我们知道一个对象的所有线程会共享它的全局变量，所以这些变量不是线程安全的，我们可以使用同步技术。但是当我们不想使用同步的时候，我们可以选择ThreadLocal变量。

每个线程都会拥有他们自己的Thread变量，它们可以使用get()\set()方法去获取他们的默认值或者在线程内部改变他们的值。ThreadLocal实例通常是希望它们同线程状态关联起来是private static属性。
## 什么是FutureTask？
**在Java并发程序中FutureTask表示一个可以取消的异步运算**。

它有启动和取消运算、查询运算是否完成和取回运算结果等方法。只有当运算完成的时候结果才能取回，如果运算尚未完成get方法将会阻塞。一个FutureTask对象可以对调用了Callable和Runnable的对象进行包装，由于FutureTask也是调用了Runnable接口所以它可以提交给Executor来执行。

## Java中interrupted 和 isInterruptedd方法的区别？
thread.interupt()： Interruption is about stopping the run of thread A with a command from thread B. This interaction is always a little random, as thread A could be at any point, halfway through some operation, at the moment that thread B decides to interrupt. Later on, we will use interruption to implement a "Stop" button that halts a bunch of worker threads.

**interrupted() 和 isInterrupted()的主要区别是前者会将中断状态清除而后者不会。**

Java多线程的中断机制是用内部标识来实现的，调用Thread.interrupt()来中断一个线程就会设置中断标识为true。当中断线程调用静态方法Thread.interrupted()来检查中断状态时，中断状态会被清零。而非静态方法isInterrupted()用来查询其它线程的中断状态且不会改变中断状态标识。简单的说就是任何抛出InterruptedException异常的方法都会将中断状态清零。无论如何，一个线程的中断状态有有可能被其它线程调用中断来改变。

## 多线程中的堆和栈
为什么把这个问题归类在多线程和并发面试题里？因为栈是一块和线程紧密相关的内存区域。

每个线程都有自己的栈内存，用于存储本地变量，方法参数和栈调用，一个线程中存储的变量对其它线程是不可见的。而堆是所有线程共享的一片公用内存区域。对象都在堆里创建，为了提升效率线程会从堆中弄一个缓存到自己的栈，如果多个线程使用该变量就可能引发问题，这时volatile 变量就可以发挥作用了，它要求线程从主存中读取变量的值。

## 线程池
创建线程要花费昂贵的资源和时间，如果任务来了才创建线程那么响应时间会变长，而且一个进程能创建的线程数有限。为了避免这些问题，在程序启动的时候就创建若干线程来响应处理，它们被称为线程池，里面的线程叫工作线程。

## Lock和synchronized对比？
- synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
- Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
- 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。

## 如何避免死锁？
Java多线程中的死锁，死锁是指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。这是一个严重的问题，因为死锁会让你的程序挂起无法完成任务，死锁的发生必须满足以下四个条件：

互斥条件：一个资源每次只能被一个进程使用。
请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
不剥夺条件：进程已获得的资源，在末使用完之前，不能强行剥夺。
循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

避免死锁最简单的方法就是**阻止循环等待条件**，将系统中所有的资源设置标志位、排序，规定所有的进程申请资源必须以一定的顺序（升序或降序）做操作来避免死锁。这篇教程有代码示例和避免死锁的讨论细节。

## Java中活锁和死锁有什么区别？
这是上题的扩展，活锁和死锁类似，不同之处在于处于活锁的线程或进程的状态是不断改变的，活锁可以认为是一种特殊的饥饿。一个现实的活锁例子是两个人在狭小的走廊碰到，两个人都试着避让对方好让彼此通过，但是因为避让的方向都一样导致最后谁都不能通过走廊。

**简单的说就是，活锁和死锁的主要区别是前者进程的状态可以改变但是却不能继续执行。**

## Thread类中的yield方法有什么作用？
Yield方法可以暂停当前正在执行的线程对象，让其它有相同优先级的线程执行。**它是一个静态方法而且只保证当前线程放弃CPU占用而不能保证使其它线程一定能占用CPU**，执行yield()的线程有可能在进入到暂停状态后马上又被执行。点击这里查看更多yield方法的相关内容。

## 为什么Thread类的sleep()和yield()方法是静态的？
一句话就是**只会作用当前正在执行的线程**，写成静态的，也不会总用到其他的thread对象上。

Thread类的sleep()和yield()方法将在当前正在执行的线程上运行。所以在其他处于等待状态的线程上调用这些方法是没有意义的。这就是为什么这些方法是静态的。它们可以在当前正在执行的线程中工作，并避免程序员错误的认为可以在其他非运行线程调用这些方法。

## ConcurrentHashMap的并发度是什么？

ConcurrentHashMap的并发度就是segment的大小，**默认为16，这意味着最多同时可以有16条线程操作ConcurrentHashMap**，这也是ConcurrentHashMap对Hashtable的最大优势，任何情况下，Hashtable能同时有两条线程获取Hashtable中的数据

## 生产消费者模型

## 详解wait/notify机制
ref: https://juejin.im/entry/5a2bb783f265da43163cff74
ref: https://stackoverflow.com/questions/3047564/java-synchronized-method-lock-on-object-or-method

![图](https://user-gold-cdn.xitu.io/2019/3/21/1699bd8849485d59?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- 线程的状态：区别**阻塞**和**等待**
	- 阻塞状态，线程等待进入synchronized代码块或方法中，等待获取锁
	- 等待状态，线程可调用wait、join等操作使自己陷入等待状态，并等待其他线程做出特定操作（如notify或中断）
- wait()方法可以使线程进入等待状态，而notify()可以使等待的状态唤醒。
- 当调用wait()方法后，线程会进入WAITING(等待状态)，后续被notify()后，并没有立即被执行，而是进入等待获取锁的阻塞队列。
- 关于监视器(monitor)：
	- wait/notify方法的调用必须处在该对象的监视器（Monitor）中，也即，在调用这些方法时首先需要获得该对象的锁。否则会抛出IllegalMonitorStateException异常。
- 在生产者线程和消费者线程中，run()之中的obj对象是一个同步块，执行各自的任务

## Multithread homework
### bank 
- 封装了3个类，Account, Bank, Transaction
- Account 里同步：**写** 这个操作
- 主要是bank
- **BlockingQueue**是一种数据结构，支持一个线程往里存资源，另一个线程从里取资源。
	- 当队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列
	- 当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒
- 每个thread需要有latch.countdown()
- main里面latch.await()，把main线程停住了，main线程就等着其他的线程全部执行countdown()方法

### cracker
- 主要思路是把一个把一个破解工作交给好几个thread来破解，A破解开头是A~E的数字，B破解开头是F~J的数字
- 用的是暴力破解，一位一位的循环匹配
- 同样的先是main thread await()，在每个worker thread中的run()方法中用latch.countdown()，等到破解完成再运行main thread
