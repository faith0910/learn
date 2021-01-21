###                             		  		java基础面试题

###### 1.为什么String要用final修饰?

a.因为它可以缓存结果，在传参时不需要考虑去改变它的值；如果是可变类的话，则可能需要拷贝出来一个新值进行传参，在性能上会有一些损失。（高效）

b.当你在调用其它方法时，比如会调用一些系统级操作指令之前，可能会有一系列的校验，如果是可变类的花话，可能在你校验之后，它的内部的值改变了，这样会引发严重的系统崩溃问题，这是迫使String类设计成不可变类的一个重要原因。（安全）

###### 2.==和equals的区别？

==对于基本数据类型来说，是用于比较"值"是否相等的；对于引用数据类型来说，是用于比较引用地址是否相同的。

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

查看Object类的源码，我们可以知道Object类中的equals()方法其实就是==，而String类重写了equals()将其变成了比较两个字符串的值是否相等。

```java
public boolean equals(Object anObject) {
    // 对象引用相同直接返回 true
    if (this == anObject) {
        return true;
    }
    // 判断需要对比的值是否为 String 类型，如果不是则直接返回 false
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            // 把两个字符串都转换为 char 数组对比
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            // 循环比对两个字符串的每一个字符
            while (n-- != 0) {
                // 如果其中有一个字符不相等就 true false，否则继续对比
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

###### 3.String和StringBuilder、StringBuffer的区别？

因为String类型是不可变的，所以在字符串拼接的时候如果使用String的话性能会很低，因此我们就需要另一个数据类型StringBuffer，它提供了append和insert方法来拼接字符串，查看源码发现，它使用Synchronized来保证线程安全，源码如下所示：

```java
@Override
public synchronized StringBuffer append(Object obj) {
    toStringCache = null;
    super.append(String.valueOf(obj));
    return this;
}

@Override
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

因为它用了Synchronized来保证线程安全，所以性能不是很高，于是在JDK1.5就有了StringBuilder，它同样提供了append和insert方法，但它没有使用Synchonized修饰，因此性能要优于StringBuffer，可以在非并发的操作环境下使用StringBuilder来拼接字符串。

###### 4.String和JVM

String创建的方式有两种，new String()和直接赋值的方式，直接赋值的会先去字符串常量池中查找是否已经存在此值，如果存在则把引用地址直接指向此值，不存在则先在常量池中创建，然后把引用指向它。而new String()的方式一定会先在堆上创建一个字符串对象，然后再去常量池中查询此字符串的值是否已经存在，如果不存在会先在常量池中创建字符串然后把引用的值指向此字符串，代码如下：

```java
String s1 = new String("Java");
String s2 = s1.intern();
String s3 = "Java";
System.out.println(s1 == s2); // false
System.out.println(s2 == s3); // true
```

它们在JVM中存储的位置，如图：

![image-20200618184916835](/Users/guanghui/Library/Application Support/typora-user-images/image-20200618184916835.png)

Tip：JDK1.7之后把永生代换成元空间，把字符串常量池从方法区移到了java堆上。# diary

###### 5、HashMap的底层实现原理是什么？

在JDK1.7中，HashMap采用数组加链表的形式去实现的，JDK1.8之后新增了红黑树的组成结构，当链表长度大于阈值8并且容量大于64时，链表结构会自动转换为红黑树结构，如下图所示:

![](/Users/guanghui/Library/Application Support/typora-user-images/image-20200622143756862.png)



数组中的元素我们称为哈希桶，它的源码如下：

```java
  static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}

```

可以看出每个hash桶柏涵四个字段：hash、key、value、next，其中next表示链表的下一个节点。JDK1.8增加红黑树是因为一旦链表过长，会严重影响HashMap的性能，而红黑树具有快速增删改查的特点，这样可以有效的解决链表过长时操作比较慢的问题。

###### 6.HashMap的源码分析

```java
// HashMap 初始化长度
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

// HashMap 最大长度
static final int MAXIMUM_CAPACITY = 1 << 30; // 1073741824

// 默认的加载因子 (扩容因子)
static final float DEFAULT_LOAD_FACTOR = 0.75f;

// 当链表长度大于此值且容量大于 64 时
static final int TREEIFY_THRESHOLD = 8;

// 转换链表的临界值，当元素小于此值时，会将红黑树结构转换成链表结构
static final int UNTREEIFY_THRESHOLD = 6;

// 最小树容量
static final int MIN_TREEIFY_CAPACITY =64;
```

**<u>什么是加载因子？加载因子为什么是0.75？</u>**

加载因子也叫扩容因子或者负载因子，用来判断什么时候进行扩容的。

至于加载因子为什么是0.75而不是其它的一些数字呢？

这其实是出于容量和性能之间平衡的结果：

a.当加载因子设置的比较大的时候，扩容的门槛就被题高了，扩容发生频率就降低了，占用的空间就比较小，但此时发生hash冲突的几率就会提升，因此需要更复杂的数据结构来存储元素，这样对元素的操作时间就会增加，运行效率就会降低；

b.当加载因子设置比较小的时候，扩容的门槛就会比较低，因此占用的空间就比较多，此时元素的存储就会变得比较稀疏，发生哈希冲突的几率就比较小，因此操作性能会比较高；

c.为了提示扩容的效率，HashMap的容量（capacity）有一个固定的要求，那就是一定是2的幂。所以当负载因子是0.75时，那么和capacity的乘积结果就可以是一个整数。

**<u>HashMap的容量为什么是2的次幂？</u>**

```java
public V get(Object key) {
    Node<K,V> e;
    // 对 key 进行哈希操作
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // 非空判断
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 判断第一个元素是否是要查询的元素
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 下一个节点非空判断
        if ((e = first.next) != null) {
            // 如果第一节点是树结构，则使用 getTreeNode 直接获取相应的数据
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do { // 非树结构，循环节点判断
                // hash 相等并且 key 相同，则返回此节点
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

​																	<!--查询方法-->

```java
public V put(K key, V value) {
    // 对 key 进行哈希操作
    return putVal(hash(key), key, value, false, true);
}
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 哈希表为空则创建表
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 根据 key 的哈希值计算出要插入的数组索引 i
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 如果 table[i] 等于 null，则直接插入
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        // 如果 key 已经存在了，直接覆盖 value
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 如果 key 不存在，判断是否为红黑树
        else if (p instanceof TreeNode)
            // 红黑树直接插入键值对
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // 为链表结构，循环准备插入
            for (int binCount = 0; ; ++binCount) {
                // 下一个元素为空时
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 转换为红黑树进行处理
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                //  key 已经存在直接覆盖 value
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    // 超过最大容量，扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

​													        ![image-20200622161801294](/Users/guanghui/Library/Application Support/typora-user-images/image-20200622161801294.png)

```java
final Node<K,V>[] resize() {
    // 扩容前的数组
    Node<K,V>[] oldTab = table;
    // 扩容前的数组的大小和阈值
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    // 预定义新数组的大小和阈值
    int newCap, newThr = 0;
    if (oldCap > 0) {
        // 超过最大值就不再扩容了
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 扩大容量为当前容量的两倍，但不能超过 MAXIMUM_CAPACITY
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    // 当前数组没有数据，使用初始化的值
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        // 如果初始化的值为 0，则使用默认的初始化容量
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    // 如果新的容量等于 0
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr; 
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    // 开始扩容，将新的容量赋值给 table
    table = newTab;
    // 原数据不为空，将原数据复制到新 table 中
    if (oldTab != null) {
        // 根据容量循环数组，复制非空元素到新 table
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                // 如果链表只有一个，则进行直接赋值
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    // 红黑树相关的操作
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    // 链表复制，JDK 1.8 扩容优化部分
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 原索引
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        // 原索引 + oldCap
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    // 将原索引放到哈希桶中
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    // 将原索引 + oldCap 放到哈希桶中
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

从以上源码可以看出，JDK1.8在扩容时并没有像JDK1.7那样，重新计算每个元素的哈希值，而是通过高位运算(e.hash&oldCap)来确定元素是否需要移动，当元素位置在扩容时发生改变时，新的位置等于原下标位置+原数组长度。

查看它的putVal的部分源码，可以看出向集合里面添加元素时，会使用(n-1)&hash的计算方法来得出该元素在集合中的位置；再看扩容源码中的部分代码，可以看出会新建一个tab，然后遍历旧的tab，将旧的元素进过e.hash & (newCap - 1)的计算添加进新的tab中，也就是(n - 1) & hash的计算方法，其中n是集合的容量，hash是添加的元素进过hash函数计算出来的hash值。

(n - 1) & hash是按位与的计算，只有当对应位置的数据都为1时，运算结果也为1，当HashMap的容量是2的n次幂时，(n-1)的2次幂也就是1111******11111这种形式的，这样与添加元素的hash值进行位运算时，能够充分的散列，使得添加的元素均匀分布在hashmap的每个位置上，减少hash碰撞，避免形成链表的结构，使得查询效率降低。

**<u>HashMap死循环分析</u>**

以 JDK 1.7 为例，假设 HashMap 默认大小为 2，原本 HashMap 中有一个元素 key(5)，我们再使用两个线程：t1 添加元素 key(3)，t2 添加元素 key(7)，当元素 key(3) 和 key(7) 都添加到 HashMap 中之后，线程 t1 在执行到 Entry<K,V> next = e.next; 时，交出了 CPU 的使用权，源码如下：

```java
void transfer(Entry[] newTable, boolean rehash) {
    int newCapacity = newTable.length;
    for (Entry<K,V> e : table) {
        while(null != e) {
            Entry<K,V> next = e.next; // 线程一执行此处
            if (rehash) {
                e.hash = null == e.key ? 0 : hash(e.key);
            }
            int i = indexFor(e.hash, newCapacity);
            e.next = newTable[i];
            newTable[i] = e;
            e = next;
        }
    }
}
```

那么此时线程 t1 中的 e 指向了 key(3)，而 next 指向了 key(7) ；之后线程 t2 重新 rehash 之后链表的顺序被反转，链表的位置变成了 key(5) → key(7) → key(3)，其中 “→” 用来表示下一个元素。

当 t1 重新获得执行权之后，先执行 newTalbe[i] = e 把 key(3) 的 next 设置为 key(7)，而下次循环时查询到 key(7) 的 next 元素为 key(3)，于是就形成了 key(3) 和 key(7) 的循环引用，因此就导致了死循环的发生，如下图所示：

![image-20200622163712233](/Users/guanghui/Library/Application Support/typora-user-images/image-20200622163712233.png)

当然发生死循环的原因是 JDK 1.7 链表插入方式为首部倒序插入，这个问题在 JDK 1.8 得到了改善，变成了尾部正序插入。

Tip:

1.HashMap采用链地址法，低十六位和高十六位异或以及hash&length-1来减少hash冲突
		2.在1.7采用头插入，1.8优化为尾插入，因为头插入容易产生环形链表死循环问题
		3.在1.7扩容位置为hash & 新容量-1，1.8是如果只有首节点那么跟1.7一样，否则判断是否为红黑树或者链表，再通过hash&原容量判断，为0放低位，否则高位，低位位置不变，高位位置=原位置+原容量
		4.树化和退树化8和6的选择，红黑树平均查找为logn，长度为8时，查找长度为3，而链表平均为8除以2；当为6时，查找长度一样，而树化需要时间；然后中间就一个数防止频繁转换
		5.容量设置为2的n次方主要是可以用位运算实现取模运算，位运算采用内存操作，且能解决负数问题；同时hash&length-1时，length-1为奇数的二进制都为1，index的结果就等同与hashcode后几位，只要hashcode均匀，那么hash碰撞就少
		6.负载因子0.75主要是太大容易造成hash冲突，太小浪费空间

###### 线程的状态有哪些？它是如何工作的？

线程是操作系统中能够运算调度的最小单位。它被包含在进程中，是进程的实际运作单位。且一个进程包含多个线程，多线程之间共享一块内存区域和一组系统资源，因此线程之间的切换更加节省资源、更加轻量化，被称为轻量化的进程。

```java
public enum State {
    /**
     * 新建状态，线程被创建出来，但尚未启动时的线程状态
     */
    NEW,

    /**
     * 就绪状态，表示可以运行的线程状态，但它在排队等待来自操作系统的 CPU 资源
     */
    RUNNABLE,

    /**
     * 阻塞等待锁的线程状态，表示正在处于阻塞状态的线程
     * 正在等待监视器锁，比如等待执行 synchronized 代码块或者
     * 使用 synchronized 标记的方法
     */
    BLOCKED,

    /**
     * 等待状态，一个处于等待状态的线程正在等待另一个线程执行某个特定的动作。
     * 例如，一个线程调用了 Object.wait() 它在等待另一个线程调用
     * Object.notify() 或 Object.notifyAll()
     */
    WAITING,

    /**
     * 计时等待状态，和等待状态 (WAITING) 类似，只是多了超时时间，比如
     * 调用了有超时时间设置的方法 Object.wait(long timeout) 和 
     * Thread.join(long timeout) 就会进入此状态
     */
    TIMED_WAITING,

    /**
     * 终止状态，表示线程已经执行完成
     */
}
```

线程的状态在JDK1.5之后以枚举的形式被定义在Thread类的源码中，它总共包括以下6个状态：

a.**NEW**,新建状态，线程被创建出来，但尚未启动时的线程状态；

b.**RUNNABLE**,就绪状态，表示可以运行的线程状态，他可能正在运行，也可能是在排队等待操作系统给她分配CPU资源；

c.**BLOCKED**,阻塞等待锁的线程状态，表示处于阻塞状态的线程正则等待监视器锁（如用synchronized标记的方法或者代码块）;

d.**WAITING**,等待状态，一个处于等待状态的线程正在等待另一个线程执行特定的动作（如一个线程调用了Object.wait()方法，那它就等待另一个线程调用Object.notify()或者Object.notifyAll()方法）；

e.**TIMED_WAITING**,计时等待状态，和等待状态类似，它只是多了超时时间，调用有超时时间设置的方法Object.wait(long timeout)和Thread.join(long timeout)它才会进入此状态；

f.**TERMINATED**,终止状态，表示线程已经执行完成。

线程的工作模式是，首先先要创建一个线程并指定线程需要执行的业务方法，然后再调用线程的start()方法，此时线程就从NEW（新建）状态变成了RUNNABLE(就绪）状态，此时线程会判断要执行的方法中有没有synchronized同步代码块，如果有并且其它线程也在使用此锁，那么线程就会变成BLOCKED（阻塞等待）状态，当其他线程使用完此锁的之后，线程会继续执行剩余的方法。

当遇到Object.wait()或Thread.join()方法时，线程会变成WAITING（等待状态）状态，如果是带了超时时间的等待方法，那么线程会进入TIMED_WAITING（计时等待）状态，当有其他方法执行了Object.notify()或者Object.notifyAll()方法之后，线程被唤醒继续执行剩余的业务方法，只到方法执行完成为止。执行流程如下图所示：

![image-20200623164121898](/Users/guanghui/Library/Application Support/typora-user-images/image-20200623164121898.png)

###### BLOCKED和WAITING的区别

首先他们的状态形成的调用方法不同，其次 BLOCKED可以理解为当前线程还处于活跃状态，只是在阻塞等待其它线程使用完某个锁资源；而WAITING则是自身调用了Object.wait()或者是Thread.join()又或是LockSupport.park()而进入等待状态，只能等待其他线程执行某个特定的动作才能被唤醒。

###### 详解ThreadPoolExecutor的参数含义和执行流程

线程池是为了避免线程频繁创建和销毁带来的性能消耗，而建立的一种池化技术，它是把已创建的线程加入"池"中，当有任务来的时候就可以重用已有的线程，无需等待创建的过程，这样可以提高程序的响应的速度。

线程池离不开ThreadPoolExecutor，线程池不允许用Executors去自动创建，而是通过ThreadPoolExecutor的方式去创建，这样做的目的是让写的读者更加明确线程池的运行规则，规避资源耗尽的风险。----《Java开发手册》

