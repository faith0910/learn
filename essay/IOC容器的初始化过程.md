##### IOC容器的初始化过程

1.Resource资源的定位（指的是BeanDefinition的资源定位，由ResourceLoader通过统一的resource接口来完成，这个resource对各种的BeanDefinition的使用提供了统一接口，采用策略模式）

2.BeanDefinition的载入（指的是用户把定义好的bean表示成ioc容器内部的数据结构，而这个数据结构就是BeanDefinition）

3.向IOC容器中注册这些BeanDefinition的过程（此过程是通过BeanDefinitionRegistry接口来实现完成的，这个注册过程是把载入过程中解析得到的BeanDefinition向ioc容器进行注册。IOC容器是通过一个HashMap来保存这些BeanDefinition数据的）

##### BeanFactory和FactoryBean的区别？

BeanFactory：一个简单的ioc容器（对象工厂），在spring中，所有的bean都是由BeanFactory来进行管理 的。

FactoryBean：一个非简单的bean，而是一个能产生或者修饰对象生成的工厂bean



