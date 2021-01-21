### mybatis运行流程：

```java
inputStream = Resources.getResourceAsStream(resource);
             // 1.创建会话工场,传入mybatis的配置文件信息
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
            
             // 2.通过工厂得到SqlSession
            sqlSession = sqlSessionFactory.openSession();
            
            // 3.通过sqlSession操作数据库
            // 第一个参数：映射文件中的statement的Id,等于namespace + "." + statement的id;
            // 第二个参数:指定和映射文件中所匹配的parameterType类型的参数;
            // sqlSession.selectOne结果是与映射文件所匹配的resultType类型的对象;
            // selectOne：查询一条结果
            User user = sqlSession.selectOne("com.mybatis.mapping.User.findUserById",1);
            。。。。
```

### SpringMVC流程

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



AOP为面向切面编程，底层是通过动态代理实现，实质上就是将相同逻辑的重复代码横向抽取出来, 拦截对象方法，对方法进行改造、增强！比如在 **方法执行前**、**方法返回后**、**方法前后**、**方法抛出异常后** 等地方进行一定的增强处理，应用场景： 事务、日志、权限、监控。

