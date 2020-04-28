# Spring学习
## Spring概述
* Spring是一个开源框架
* Spring为简化企业级应用开发而生,主要解决业务逻辑层和其他各层松耦合问题,面向接口的编程思想
* Spring是javaSE/EE的一站式框架. web层SpringMVC,业务层SpringIoc、事务机制,持久层提供Spring-jdbc或者直接整合现有的框架（mybatis）

## Web层SpringMVC
### springmvc运行原理
* 用户发送请求到前端控制器DispatcherServlet
* DispatcherServlet收到请求调用HandlerMapping处理映射器
* 处理映射器找到具体的处理器(根据xml进行配置,注解进行查找)生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
* DispatcherServlet调用HandlerAdapter处理器适配器。
* HandlerAdapter经过适配调用具体的处理器(Controller,后端处理器)
* Controller执行完ModelAndView.
* HandlerAdapter将Controller执行结果的ModelAndView返回给DispatchServlet
*  DispatcherServlet将ModelAndView传给ViewReslover视图解析器
* ViewReslover解析后返回具体View
* DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）
* DispatcherServlet响应用户

![springMvc运行原理](springMvc%E8%BF%90%E8%A1%8C%E5%8E%9F%E7%90%86.jpg)
### 主要注解
- @Controller 标注控制层组件,使用它标记的类就是一个SpringMVC Controller对象
- @RestController 相当于@Controller和@ResponseBody的组合效果
- @Component 泛指组件.当组件不好归类时,可以使用这个注解标注
- @Service 标注业务层注解
- @Repository 用来注解dao层,在daoImpl累上面的注解
- @ResponeBody 异步请求,将Controller转化为json格式
- @RequestMapping 一个用来处理请求地址的映射,用于类或方法上
- @Autowired 可以对类成员变量,方法及构造方法函数进行标注,完成自动装配工作.
- @PathVariable 用于将请求的url变量映射到功能处理方法参数上
- @RequestParam 主要用于SpringMVC后台控制层获取参数
- @RequestHeader 可以把request请求header部分的值绑定到方法参数
### web.xml配置
* 配置监听器
   - ContextLoaderListener作用是启动web容器,(加载配置文件)自动装配applicationContext.xml配置信息
   - spring log4j日志监听器配置

```java
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
</listener>
```

* 部署applicationContext.xml文件
   * 如果不写任何参数配置,默认的是在/WEB-INF/applicationContext.xml
   * 如果想要自定义文件名，需要在web.xml中加入contextConfigLocation这个context参数

```
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext*.xml</param-value>
</context-param>
```
* 配置前段控制器DispatcherServlet

```java
<servlet>
　　<servlet-name>DispatcherServlet</servlet-name>
　　<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
　　<init-param>
　　　　<!-- 指定SpringMVC配置文件 -->
　　　　<param-name>contextConfigLocation</param-name>
　　　　<param-value>classpath*:config/springmvc.xml</param-value>
　　</init-param>
</servlet>
<servlet-mapping>
　　<!-- 设置http请求拦截，如*.do，这里设置的是拦截所有 -->
　　<servlet-name>DispatcherServlet</servlet-name>
　　<url-pattern>/</url-pattern>
</servlet-mapping>
```
* 配置字符集编码

```java
  <filter>
    <!-- 用来对浏览器的每一次请求进行过滤，加上了父类没有的功能，就是设置字符集编码，一般只用来配置字符集 -->
       <filter-name>CharacterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
          <param-name>encoding</param-name>
          <param-value>utf-8</param-value>
       </init-param>
       <init-param>
       <!-- forceEncoding用来设置是否理会 request.getCharacterEncoding的方法 -->
          <param-name>forceEncoding</param-name>
          <param-value>true</param-value>
       </init-param>
    </filter>
    <filter-mapping>
       <filter-name>CharacterEncodingFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```
* 错误跳转页面
```java
<!-- 错误跳转页面 -->
     <error-page>
      <!-- 路径不正确 -->
     <error-code>404</error-code>
      <location>/WEB-INF/errorpage/404.jsp</location>
     </error-page>
     
     <error-page>
      <!-- 没有访问权限，访问被禁止 -->
      <error-code>405</error-code>
      <location>/WEB-INF/errorpage/405.jsp</location>
     </error-page>
     
     <error-page>
      <!-- 内部错误 -->
      <error-code>500</error-code>
      <location>/WEB-INF/errorpage/500.jsp</location>
     </error-page>
```

## 业务层SpringIOC,SpringAOP

### SpringIOC
* IOC 依赖反转
将原本在程序中手动创建对象的权利交给spring框架管理,创建对象的权利被反转到了spring框架上
* DI 依赖注入
spring创建这个对象,将其依赖的属性注入进去
```java
BEAN 创建
-- userService interface
public interface UserService{
   public void sayHello();
}
-- userServiceImpl userService实现类
public class UserServiceImpl implements UserService {
    private String name;
    @Override
    public void sayHello() {}
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
-- spring application.xml(userService的创建权交给spring)
<bean id="userService" class="com.ioc.demo1.UserServiceImpl">
  <property name="name" value="xxx"></property> 
</bean>
```

```java
BEAN 调用
// spring 调用 
UserService userService = new UserServiceImpl();
public void test1(){
    userService.sayHello();
}

// 配置文件 调用 
public void test2(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserService userService = (UserService) applicationContext.getBean("UserService");
    userService.sayHello();
}
```

### SpringAOP
#### AOP
面向切面编程
* Aspect: 切面 切面一般定义为一个java类,每个切面侧重于特定的跨领域功能.例如:日志打印,事务管理
* JoinPoint: 连接点 程序执行的某个点，比如方法执行。构造函数调用或者字段赋值等。
* Advice：通知 在连接点要的代码
* PointCut: 切点 一个匹配连接点的正则表达式。当一个连接点匹配到切点时，一个关联到这个切点的特定的 通知 (Advice) 会被执行
* Weaving: 编织 负责将切面和目标对象链接，以创建通知对象，在 Spring AOP 中没有这个东西。
#### SpringAOP
SpringAOP面向切面编程,主要作用与项目的日志记录,事务管理,性能统计,安全控制,异常处理等
* SpringAop .xml配置
```xml
<!-- aop -->   
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <!-- 数据源dataSource在applicationContext-dao.xml中配置了-->
  <property name="dataSource" ref="dataSource"/>
</bean>

<!--配置通知 --> 
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="add*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
        <tx:method name="del*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
        <tx:method name="update*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
        <tx:method name="*" propagation="REQUIRED" read-only="true"/>
    </tx:attributes>
</tx:advice>

<!-- 要扫描的serviceImpl包 aop -->
<aop:config>
    <aop:advisor pointcut="execution(* com.example.demo.*.service.*.*(..))" advice-ref="txAdvice"/>
</aop:config>
```
* SpringAop 注解使用
   - @Aspect 定义切面
   - @Pointcut(“excution(* com.example.demo.*.service.*.*(..))”)
   - @Before
      *  在增强的方法上@Before("execution(* 包名.*(..))")或使用切点@Before("pointcut()")
   - @After(“excution(...)”) 在切点后面执行 用法和@Before一样
   - @Around
      * 在增强的方法上@Around("execution(* 包名.*(..))")或使用切点@Around("pointcut()")
      * 接收参数类型为ProceedingJoinPoint，返回值为Object
      * 需要执行ProceedingJoinPoint对象的proceed方法，在这个方法前与后面做环绕处理，可以决定何时执行与完全阻止方法的执行
      * @Around相当于@Before和@AfterReturning功能的总和
   - @AfterRetruning: 在方法返回之前，获取返回值并进行记录操作
   - @AfterThrowing: 在异常抛出前进行处理，比如记录错误日志
```java
@Aspect 
public class TransactionManager {

    @Pointcut("execution(* com.example.demo.*.service.*.*(..))")
    public void testPoint(){
    }

    @Before("execution(* com.example.demo.*.service.*.*(..))")
    public void testBefore(){
        System.out.println("前置通知");
    }

    @Around("execution(* com.example.demo.*.service.*.*(..))")
    Object auth(ProceedingJoinPoint point) {

        Object object = null;
        try {
            System.out.println("事务开启……");
            //放行
            object = point.proceed();
            System.out.println("事务关闭……");
        } catch (Throwable e) {
            e.printStackTrace();
        }
        return object;
    }

    @AfterThrowing(throwing="ex1", pointcut="testPoint()")
    public void testThrowing(IOException ex1){
        System.out.println("输出异常" + ex1);
    }

    @AfterReturning(returning="rvt", pointcut="testPoint()")
    public void log(Object rvt) {
        System.out.println("获取目标返回值："+ rvt);
}

```

## 持久层Spring-JDBC结合其他框架
## 设计模式
