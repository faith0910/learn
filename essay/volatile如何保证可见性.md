volatile如何保证可见性

被volatile修饰的共享变量进行写操作时会多出加了lock的汇编代码，lock指令在多核处理器下会依法两件事：

1.讲当前处理器缓存行的数据写回到系统内存

2.这个写回内存的操作会使在其它cpu里缓存了该内存地址的数据无效

java对象头

Mark Word （对象的hashcode、分代年龄、锁信息）、ClassMetadataAddress、Array Length

偏向锁

当一个线程访问同步块并获取锁时，会在对象头和栈帧的锁记录里边存储偏向锁的线程ID，以后该线程在进入和退出同步块是不需要进行CAS操作来加解锁，只需要测试一下对象头中的Mark Word中是否存储着指向该线程的偏向锁。若是测试成功，表示线程已经获得了锁；若是测试不成功，则需要在测试一下Mark Word中偏向锁的标志是否设置为1（表示当前已经处于偏向锁状态）：若是没有设置，则使用CAS锁竞争机制来竞争锁；若是设置了，则尝试使用CAS将对象头的偏向锁指向当前线程。

可以使用 -XX:BiasedLockingStartupDelay=0设置立即启动。可以使用 -XX:-UsebiasedLocking-false关闭偏向锁

