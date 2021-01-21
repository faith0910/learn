#### 								                                  May everything you meet be gentle！

##### 1.spring、springmvc、springboot、springcloud的区别？

sring是核心，它提供了基础功能，它是一个生态。

springmvc是一个基于spring的mvc框架。

springboot是为了简化spring配置而衍生的快速开发脚手架。

springcloud是构建在springboot上的服务治理框架。

##### 2.spring中bean的生命周期？

a.实例化bean

b.依赖注入

c.ifbean实现beanNameAware接口，调用setBeanName()为bean设置名称。

d.ifbean实现beanFactoryAware接口，调用setBeanFactory()为bean设置beanFactory。

e.ifbean实现applicationContextAware接口，调用setApplicationContext()为bean加载spring上下文。

f.ifbean实现beanPostProcessor接口，调用postProcessorBeforeInitialization()

g.ifbean实现了sping配置文件中的init-method属性，会自动调用其配置初始化方法。

h.ifbean实现beanPostProcessor接口，调用postProcessorAfterInitialization()。

i.当bean不再需要时，会经历清理阶段，if实现了DisposableBean接口，会调用destory()。

j.如果这个bean配置了destory-method属性，会自动调用其销毁方法。

##### 3.拦截器和过滤器的区别？

拦截器基于反射机制实现的，过滤器基于函数回掉实现的

拦截器不依赖servlet容器，过滤器依赖

拦截器只对action起作用，而过滤器几乎对所有请求都起作用

在action生命周期中，拦截器可以多次调用，而过滤器只在容器初始化过程中调用一次

拦截器可以访问action上下文，值栈中的数据，而过滤器不可以

##### 4.SpringMVC流程

1、 用户发送请求至前端控制器DispatcherServlet。

2、 DispatcherServlet收到请求调用HandlerMapping处理器映射器。

3、 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。

4、 DispatcherServlet调用HandlerAdapter处理器适配器。

5、 HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。

6、 Controller执行完成返回ModelAndView。

7、 HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。

8、 DispatcherServlet将ModelAndView传给ViewReslover视图解析器。

9、 ViewReslover解析后返回具体View。

10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。

11、 DispatcherServlet响应用户。

##### 5.为什么String要用final修饰?

a.因为它可以缓存结果，在传参时不需要考虑去改变它的值；如果是可变类的话，则可能需要拷贝出来一个新值进行传参，在性能上会有一些损失。（高效）

b.当你在调用其它方法时，比如会调用一些系统级操作指令之前，可能会有一系列的校验，如果是可变类的花话，可能在你校验之后，它的内部的值改变了，这样会引发严重的系统崩溃问题，这是迫使String类设计成不可变类的一个重要原因。（安全）

##### 6.HashMap为什么是线程不安全的？

a.在jdk1.7中，在多线程环境下，扩容时会造成环形链或者数据丢失。

b.在jdk1.8中，在多线程环境下，会发生数据覆盖的情况。

##### 7.HashMap实现原理

简单来说，HashMap是由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null），那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n),首先遍历链表，存在即覆盖，否则新增；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，HashMap的链表出现的越少，性能才会越好。

##### 8.线程与进程的区别？

a.进程是操作系统进行资源调度的基本单位,线程是程序执行的基本单位

b.进程有自己的独立地址空间，线程没有独立的地址空间

c.CPU切换一个线程毕切换进程的花费小

d.创建一个线程比进程的开销小

e.线程占用的资源比进程少

f.线程之间通信更方便，同一个进程下，线程共享全局变量，静态变量等数据，进程之间的通信需要以通信的方式（IPC）进行

g.进程对资源保护要求高，开销大，效率相对较低，线程资源保护要求不高，但开销小，效率高，可频繁切换

##### 

##### 

##### 



