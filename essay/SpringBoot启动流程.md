1.SpringBoot的启动过程

```java
public ConfigurableApplicationContext run(String... args) {
 				//1.创建并启动一个计时监控类
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
   			//2.初始化应用上下文和异常报告集合
        ConfigurableApplicationContext context = null;
        Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
   			//3.设置系统属性 `java.awt.headless` 的值，默认值为：true
        this.configureHeadlessProperty();
   			//4.创建所有spring 运行监听器并发布应用启动事件
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
        listeners.starting();

        Collection exceptionReporters;
        try {
          	//5.初始化默认应用参数类
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
          	//6.根据运行监听器和应用参数来准备spring环境
            ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments);
            this.configureIgnoreBeanInfo(environment);
          	//7.打印Banner类
            Banner printedBanner = this.printBanner(environment);
          	//8.创建应用上下文
            context = this.createApplicationContext();
          	//9.准备异常报告器
            exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class, new Class[]{ConfigurableApplicationContext.class}, context);
          	//10.准备应用上下文
            this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);
          	//11.刷新应用上下文
            this.refreshContext(context);
          	//12.刷新应用上下文后置处理
            this.afterRefresh(context, applicationArguments);
          	//13.停止计时监控类
            stopWatch.stop();
          	//14.输出日志记录执行主类名、时间信息
            if (this.logStartupInfo) {
                (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
            }
						//15.发布应用上下文启动完成事件
            listeners.started(context);
          	//16.执行所有runner运行器
            this.callRunners(context, applicationArguments);
        } catch (Throwable var10) {
            this.handleRunFailure(context, var10, exceptionReporters, listeners);
            throw new IllegalStateException(var10);
        }

        try {
          	//17.发布上下文就绪事件
            listeners.running(context);
          	//18.返回上下文
            return context;
        } catch (Throwable var9) {
            this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null);
            throw new IllegalStateException(var9);
        }
    }
```

2.SpringBoot包含什么？

A.起步依赖

B.自动配置

C.SpringBoot CLI

D.SpringBoot Actuator

