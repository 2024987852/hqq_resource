# SSM注解汇总

[toc]

# MyBatis注解

## 1. @param("param1","param2",...)

标识mapper接口中的方法参数

- 以@Param注解的**value属性值为键**，以**参数为值**；

- 以param1,param2...为键，以参数为值；

```java
User getUserById(@Param("id") int id);
List<User> getUserByTable(@Param("tableName") String tableName);
```

## 2. @MapKey("id")

- 查询出的数据有**多条**
- 在mapper接口的方法上添加@MapKey注解
- **@MapKey注解设置map集合的键，值是每条数据所对应的map集合**

```java
/**
 * 查询所有用户信息为map集合
 * @return
 * 将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，并且最终要以一个map的方式返回数据，此时需要通过@MapKey注解设置map集合的键，值是每条数据所对应的map集合
 */
@MapKey("id")
Map<String, Object> getAllUserToMap();
```

# Spring注解

## 1. IOC @Component @Service @Controller @Repository

Spring针对Bean管理中创建对象提供注解（实质都一样）
（1）@Component  普通注解
（2）@Service 		  一般用于Service层
（3）@Controller 	 一般用于web层（Controller）
（4）@Repository	 一般用于持久层（DAO）

```java
//在注解里面 value 属性值可以省略不写， 
//默认值是类名称，首字母小写 
//UserService -- userService 
@Component(value = "userService")  //<bean id="userService" class=".."/> 
public class UserService { 
    public void add() { 
        System.out.println("service add......."); 
    } 
}
```

注解使用时，要开启注解扫描：

```xml
<!--示例 1 
    use-default-filters="false" 表示现在不使用默认filter，自己配置 filter 
    context:include-filter ，设置扫描哪些内容 
--> 
<context:component-scan base-package="com.atguigu" use-default- filters="false"> 
    <context:include-filter type="annotation" 
expression="org.springframework.stereotype.Controller"/>
</context:component-scan> 
 
<!--示例 2 
    下面配置扫描包所有内容 
    context:exclude-filter： 设置哪些内容不进行扫描 
--> 
<context:component-scan base-package="com.atguigu"> 
    <context:exclude-filter type="annotation" 
                            expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

## 2. 基于注解方式实现属性注入

### 1）@Autowired

根据属性类型进行自动装配

1. 把service和dao对象创建，在service和dao类添加创建对象注解
2. 在service注入dao对象，在service类添加dao类型属性，在属性上面使用注解

```java
@Service
public class UserService { 
    //定义 dao 类型属性 
    //不需要添加 set 方法 
    //添加注入属性注解 
    @Autowired 
    private UserDao userDao;
    public void add() { 
        System.out.println("service add.......");
        userDao.add(); 
    } 
}
```

### 2）@Qualifier

根据名称进行注入，@Qualifier注解的使用，和上面@Autowired一起使用

```java
//定义 dao 类型属性 
//不需要添加 set 方法 
//添加注入属性注解 
@Autowired  //根据类型进行注入 
@Qualifier(value = "userDaoImpl1") //根据名称进行注入 
private UserDao userDao;
```

### 3）@Resource

可以根据类型注入，也可以根据名称注入

```java
//@Resoure //根据类型进行注入
@Resource(name = "userDaoImpl1") //根据名称进行注入 
private UserDao userDao;
```

### 4）@Value

注入普通类型属性

```java
@Value(value = "abc")
private String name;
```

## 3. IOC完全注解开发

### 1）@Configuration

配置文件 ，创建配置类，替代xml配置文件

### 2）@ComponentScan

开启组件扫描

```java
@Configuration
配置文件 
@ComponentScan(basePackages = {"com.atguigu"}) public class SpringConfig { 
}
```

### 3）@Bean

-  需要在配置类中使用，即类上需要加上@Configuration注解
- @Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理
- Spring将会将这个Bean对象放在自己的IOC容器中

```java
@Configuration
public class WebSocketConfig {
    @Bean
    public Student student(){
        return new Student();
    }
}
```



## 4. AOP AspectJ注解

### 1）@Aspect

在增强类里面，创建方法，让不同方法代表不同通知类型

- 生成代理对象Proxy

（1）切入点表达式作用：知道对哪个类里面的哪个方法进行增强
（2）语法结构：` execution([权限修饰符] [返回类型] [类全路径] [方法名称]([参数列表]) )`

  举例1：对com.atguigu.dao.BookDao类里面的add进行增强
  `execution(* com.atguigu.dao.BookDao.add(..))`

  举例2：对com.atguigu.dao.BookDao类里面的所有的方法进行增强
  `execution(* com.atguigu.dao.BookDao.* (..))`

  举例3：对com.atguigu.dao包里面所有类，类里面所有方法进行增强
  `execution(* com.atguigu.dao.*.* (..))`

### 2）@Before(value=切入点表达式)

前置通知（增强），其中value可以省略

### 3）@AfterReturning(value=切入点表达式)

后置通知（增强）

### 4）@After(value=切入点表达式)

最终通知（增强）

### 5）@AfterThrowing(value=切入点表达式)

异常通知（增强）

### 6）@Around(value=切入点表达式)

环绕通知（增强）

```java
//增强的类 
@Component 
@Aspect  //生成代理对象 
public class UserProxy { 
    //前置通知 
    //@Before 注解表示作为前置通知 
    @Before(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void before() { 
        System.out.println("before........."); 
    } 
    //后置通知（返回通知） 
    @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))") 
    public void afterReturning() { 
        System.out.println("afterReturning........."); 
    } 
    //最终通知 
    @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))") 
    public void after() { 
        System.out.println("after........."); 
    } 
    //异常通知 
    @AfterThrowing(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))") 
    public void afterThrowing() { 
        System.out.println("afterThrowing........."); 
    } 
    //环绕通知 
    @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable { 
        System.out.println("环绕之前........."); 
        //被增强的方法执行 
        proceedingJoinPoint.proceed(); 
        System.out.println("环绕之后........."); 
    } 
}
```

### 7）@Pointcut(value=切入点表达式)

植入Advice(通知)的触发条件

```java
//相同切入点抽取 
@Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")

public void pointdemo() {
} 
//前置通知 
//@Before 注解表示作为前置通知 @Before(value = "pointdemo()") public void before() { 
    System.out.println("before.........");
}
```

### 8）@Order(数字类型值)

- 有多个增强类对同一个方法进行增强，设置增强类优先级
- 在增强类上面添加注解 **@Order(数字类型值)**，数字类型值越小优先级越高

```java
@Component
@Aspect 
@Order(1)
public class PersonProxy
```

### 9）@EnableAspectJAutoProxy(proxyTargetClass = true)

- AOP完全注解开发
- 开启切面支持

```java
@Configuration
@ComponentScan(basePackages = {"com.atguigu"})
@EnableAspectJAutoProxy(proxyTargetClass = true) 
public class ConfigAop { 
}
```

## 5. 事务管理

### 1）@Transactional

在service类上面（或者service类里面方法上面）添加事务注解
（1）@Transactional，这个注解添加到类上面，也可以添加方法上面
（2）如果把这个注解添加类上面，这个类里面所有的方法都添加事务
（3）如果把这个注解添加方法上面，为这个方法添加事务

```java
@Service 
@Transactional 
public class UserService {
}
```

事务相关参数:

1. propagation：事务传播行为
2. ioslation：事务隔离级别（脏读、不可重复读、虚（幻）读）
3. timeout：超时时间（默认值是 -1 ，设置时间以秒单位进行计算；在一定时间内进行提交，如果不提交进行回滚）
4. readOnly：是否只读(readOnly默认值false，表示可以查询，可以添加修改删除操作；设置readOnly值是true，设置成true之后，只能查询)
5. rollbackFor：回滚(设置出现哪些异常进行事务回滚)
6. noRollbackFor：不回滚(设置出现哪些异常不进行事务回滚)

![image-20221105144725407](SSM注解汇总.assets\image-20221105144725407.png)

![image-20221105144834477](SSM注解汇总.assets\image-20221105144834477.png)



### 2）完全注解声明式事务管理

创建配置类，使用配置类替代xml配置文件

```java
@Configuration //配置类 
@ComponentScan(basePackages = "com.atguigu") //组件扫描 
@EnableTransactionManagement //开启事务 
public class TxConfig { 
    //创建数据库连接池 
    @Bean 
    public DruidDataSource getDruidDataSource() { 
        DruidDataSource dataSource = new DruidDataSource(); 
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver"); 
        dataSource.setUrl("jdbc:mysql:///user_db"); 
        dataSource.setUsername("root"); 
        dataSource.setPassword("123456"); 
        return dataSource; 
    } 
    //创建 JdbcTemplate 对象 
    @Bean 
    public JdbcTemplate getJdbcTemplate(DataSource dataSource) {
        //到 ioc 容器中根据类型找到dataSource 
        JdbcTemplate jdbcTemplate = new JdbcTemplate(); 
        //注入 dataSource 
 		jdbcTemplate.setDataSource(dataSource);
		return jdbcTemplate;
    } 

    //创建事务管理器 
    @Bean 
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource) { 
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager(); 
        transactionManager.setDataSource(dataSource); 
        return transactionManager; 
    } 
}
```

## 6. Spring5框架新功能

### 1）@Nullable

- @Nullable注解可以使用在**方法上面，属性上面，参数上面**，表示方法返回可以为空，属性值可以为空，参数值可以为空

### 2）@SpringJUnitConfig

- 支持整合JUnit5
- 使用一个复合注解替代传统两个注解完成整合

传统方法：

- 第一步 引入JUnit5的jar包

![image-20221105145716118](SSM注解汇总.assets\image-20221105145716118.png)

- 第二步 创建测试类，使用注解完成

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:bean1.xml")
public class JTest5 { 
    @Autowired 
    private UserService userService; 
    @Test 
    public void test1() { 
        userService.accountMoney(); 
    } 
}
```

新功能：

使用一个复合注解替代上面两个注解完成整合

```java
@SpringJUnitConfig(locations = "classpath:bean1.xml")
public class JTest5 { 
    @Autowired 
    private UserService userService;
    @Test 
    public void test1() { 
        userService.accountMoney(); 
    } 
}
```



# SpringMVC注解

## 1. @RequestMapping

- 处理请求和控制器方法之间的映射关系
-  @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径

```java
@RequestMapping("/")
public String index() {
    //设置视图名称
    return "index";
}

@RequestMapping("/hello")
public String HelloWorld() {
    return "target";
}
```

参数配置：

- value属性
- method属性
- params属性（了解）
- headers属性（了解）

```java
@RequestMapping(
        value = {"/testRequestMapping", "/test"},
        method = {RequestMethod.GET, RequestMethod.POST}
)
public String testRequestMapping(){
    return "success";
}
```

## 2. @PathVariable

- 将占位符所表示的数据赋值给控制器方法的形参

```html
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a><br>
```

```java
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
//最终输出的内容为-->id:1,username:admin
```

## 3. @RequestParam

- 将请求参数和控制器方法的形参创建映射关系
- @RequestParam("user_name") String username

参数配置：

- value：指定为形参赋值的请求参数的参数名
- **required：设置是否必须传输此请求参数**，默认值为true
  - 若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；
  - 若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null
- defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

## 4. @RequestHeader

- @RequestHeader是将请求头信息和控制器方法的形参创建映射关系
- @RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

## 5. @CookieValue

- @CookieValue是将cookie数据和控制器方法的形参创建映射关系
- @CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

## 6. @RequestBody

- 获取请求体，需要在控制器方法设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值

```java
@RequestMapping("/testRequestBody")
public String testRequestBody(@RequestBody String requestBody){
    System.out.println("requestBody:"+requestBody);
    return "success";
}
```

输出结果：

requestBody:username=admin&password=123456

## 7. @ResponseBody(常用)

- 标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器，**不作为视图响应**

```java
@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    return "success";
}
```

结果：浏览器页面显示success

## 8. @RestController

- 是springMVC提供的一个复合注解
- 标识在控制器的类上，就相当于为类**添加了@Controller注解**，并且为其中的每个方法**添加了@ResponseBody注解**

## 9. @ControllerAdvice  @ExceptionHandler

- @ControllerAdvice标识为异常处理的组件
- @ExceptionHandler用于设置所标识方法处理的异常

```java
@ControllerAdvice
public class ExceptionController {
    //@ExceptionHandler用于设置所标识方法处理的异常
    @ExceptionHandler(ArithmeticException.class)
    //ex表示当前请求处理中出现的异常对象
    public String handleArithmeticException(Exception ex, Model model){
        model.addAttribute("ex", ex);
        return "error";
    }
}
```

## 10. 注解配置SpringMVC

使用配置类和注解**代替web.xml和SpringMVC配置文件**的功能

### 1、创建初始化类，代替web.xml

在Servlet3.0环境中，容器会在**类路径src**中查找实现javax.servlet.ServletContainerInitializer接口的类，如果找到的话就用它来配置Servlet容器(Tomcat服务器)。
Spring提供了这个接口的实现，名为SpringServletContainerInitializer，这个类反过来又会查找实现WebApplicationInitializer的类并将配置的任务交给它们来完成。Spring3.2引入了一个便利的WebApplicationInitializer基础实现，名为AbstractAnnotationConfigDispatcherServletInitializer，当我们的类扩展了AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自动发现它，并用它来配置Servlet上下文。

```java
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {

    /**
     * 指定spring的配置类
     * @return
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    /**
     * 指定SpringMVC的配置类
     * @return
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    /**
     * 指定DispatcherServlet的映射规则，即url-pattern
     * @return
     */
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * 添加过滤器
     * @return
     */
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
        encodingFilter.setEncoding("UTF-8");
        encodingFilter.setForceRequestEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
    }
}
```

### 2、创建SpringConfig配置类，代替spring的配置文件

```java
@Configuration
public class SpringConfig {
	//ssm整合之后，spring的配置信息写在此类中
}
```

### 3、创建WebConfig配置类，代替SpringMVC的配置文件

1、扫描组件			2、视图解析器			3、view-controller	4、default-servlet-handler（静态资源处理）

5、mvc注解驱动	6、文件上传解析器	 7、异常处理				8、拦截器

```java
//将当前类标识为一个配置类
@Configuration
//1、扫描组件
@ComponentScan("com.atguigu.mvc.controller")
//5、开启MVC注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    //4、使用默认的servlet处理静态资源，default-servlet-handler
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //6、配置文件上传解析器
    @Bean
    public CommonsMultipartResolver multipartResolver(){
        return new CommonsMultipartResolver();
    }

    //8、配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        FirstInterceptor firstInterceptor = new FirstInterceptor();
        registry.addInterceptor(firstInterceptor).addPathPatterns("/**");
    }
    
    //3、配置视图控制，view-controller
    
    /*@Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }*/
    
    //7、配置异常映射
    /*@Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
        Properties prop = new Properties();
        prop.setProperty("java.lang.ArithmeticException", "error");
        //设置异常映射
        exceptionResolver.setExceptionMappings(prop);
        //设置共享异常信息的键
        exceptionResolver.setExceptionAttribute("ex");
        resolvers.add(exceptionResolver);
    }*/

    //2、配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext 的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(
                webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }

    //2、生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }

    //2、生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
}
```
