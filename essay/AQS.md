##### AQS的一些内容

1.Semaphore（信号量又被称为信号灯，在多线程下用于协调各个线程，以保证它们能够正确合理的使用资源。

acquire():线程通过它获取一个许可，然后对共享资源进行操作，如果许可集已经分配完了，那么线程进入等待状态，直到其它线程释放许可才可以再次获取许可。

release():线程释放一个许可，许可将被归还给Semphore。

2.CyclicBarrier：它类似于CountDownLatch，它能阻塞一组线程直到某个事件发生。CyclicBarrier与CountDownLatch的区别关键在于，所有事件必须同时到达栅栏处，才能继续进行。CountDownLatch用于等待事件，CyclicBarrier用于等待其它线程。

await():调用await方法的线程告诉CyclicBarrier它已经到达同步点，然后当前线程被阻塞。CyclicBarrier同样提供带超时时间和不带超时时间的await方法。

3.CountDownLatch：通过一个计数器实现，计数器的初始值是当前线程的数量。每当一个线程执行完毕后，技术器就执行-1操作，当计数器值为0时，所以线程都执行完毕。

await():调用了await方法的线程会被挂起，它会等待count为0才会继续执行，await(long timeout,TimeUnit unit)zhe'ge这个带超时时间的方法表示当等待一定时间后count还没为0就继续执行。

qiang'zhancountdown():对count的值进行减1操作。

4.ReentrantLock:https://mp.weixin.qq.com/s/dM6ITD2ecRdgsriQYbtxfQ

锁实现：a.公平锁       每个线程公平获取锁，不会出现线程饥饿的状况。

​				b.非公平锁   抢占式获取锁，容易出现线程饥饿状况。

Condition：多线程协调通信类

​			condition维护了一个线程等待队列，也是多线程协调通信的工具类，使得某个线程一起等待某个条件，满足条件后才能唤醒，唤醒后还需要重新竞争锁。

​			ReentrantLock和condition一起实现了生产者消费者模式。

ReentrantLock和Synchronized的区别？

存在层面：Synchronized是一个Java中的关键字，存在于JVM层面，而ReentrantLock是一个接口。

锁的释放条件：1.获取锁的线程执行完同步代码块，自动释放；2.线程发生异常时，JVM会让线程释放锁。Lock必须在finally中释放锁，不然容易造成死锁。

锁的获取：在Synchronized中，假设线程A获得锁，B线程等待。如果A发生阻塞，B会一直等待。在Lock中，分情况而定，Lock有尝试获取锁的方法，如果尝试获取到锁，则不用一直等待。

锁状态：Synchronized无法判断锁状态，Lock 则可以判断

锁类型：Synchronized可重入，不可中断，非公平锁；Lock是可重入，可中断，可公平锁

锁性能：Synchronized适用于少量同步的状态下，性能开销比较大。Lock锁适用于大量同步的阶段。

在竞争不是很激烈的情况下，Synchronized的性能要优于ReetrantLock，但是在资源竞争很激烈的情况下，Synchronized的性能会下降几十倍，但是ReetrantLock的性能能维持常态；

ReetrantLock 提供了多样化的同步，比如有时间限制的同步，可以被Interrupt的同步（synchronized的同步是不能Interrupt的）等

5.ReentrantReadWriteLock:JUC包下提供了读写锁，它表示两个锁，一个是读操作相关的锁，称共享锁；一个是写相关的锁，称为排他锁。

读写锁的三个重要特性：

a.公平选择性：支持非公平（默认）和公平的锁获取方式，吞吐量非公平锁优于公平锁。

b.重入：读锁和写锁都支持可重入。

c.锁降级：遵循获取写锁，获取读锁再释放写锁的次序，写锁能够重降级为读锁。

