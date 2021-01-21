bean的生命周期？

 

实例化bean

 

依赖注入

 

If bean实现了beanNameAware接口，调用setBeanName方法

 

If bean实现了beanFactoryAware接口。调用setBeanFactory方法

 

If bean实现了applicationContextAware接口，调用setApplicationContext方法

 

If bean实现了beanPostPocessor接口，调用postPocessorBeforeInitialization方法

 

If bean实现了spring配置文件中的init-method属性时，会自动调用其配置的初始化方法

If bean实现了beanPostprocessor接口，调用postPocessorAfterinitialization方法

 

当bean不在需要时，回历经清理阶段，if bean实现了DisposableBean接口，会调用destory方法

 

If bean配置了destory-method属性，会自动调用其销毁方法。

 

 

 

​	