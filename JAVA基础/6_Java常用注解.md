# Java常用注解
- @Controller : 当前的类为控制器
- @ResponseBody : 以json的格式返回数据
- @RestController : @Controller + @ResponseBody
- @Autowired : 自动装配,将其他的类，接口引入
- @RequestMapping : 配置请求url映射,如果没有指定请求方式，将接收所有的请求方式
- @Post : 配置请求url映射,以POST请求方式
- @PathVaribale : 获取请求url映射,中的地址的值
- @RequestParam : 获取请求参数,可以通过defaultValue 属性设置不传入的默认值,适合单层简单对象
- @RequestBody : 获取请求参数,将body转化为后台对象
- @Service : 标识为service层
- @Override : 伪代码,校验重写
- @Repository : 标识Dao层
- @Dao : 标识Dao层
- @Component  : 没有明确的角色
- @Async   : 异步调用
- @Resource   : 自动装配,将其他的类，接口注入
- @Bean   : 声明当前方法的返回值为一个bean(可以配置属性)
- @Aspect   : 标识为切面类
- @PointCut   : 声明切点
- @Before: 前置通知,在做某件事之前做的事。
- @After: 后置通知,在做某件事之后做的事。
- @AfterReturning: 返回后通知,在执行结束之前执行,如果异常将不执行,通常用于对其返回值做增强处理。
- @AfterThrowing: 异常通知,在做某件事抛出异常时，执行。
- @Around: 环绕通知,在做某件事前后都执行，异常后不执行后面。
- @Value: 为属性注入值,:表示设置默认值,支持表达式,文件。
- @EnableAsync: 启动类开启异步。
- @EnableScheduling: 启动类开启定时任务。
- @Scheduled : 表名定时任务。
- @RunWith  : SpringBoot测试运行容器注解。
- @ContextConfiguration   : 用来加载配置ApplicationContext。
- @ControllerAdvice    : 全局异常处理类。
- @ExceptionHandler    : 全局异常处理处理的异常。
- @ComponentScan    : 用于指定扫包范围不指定默认为当前类所在的包。
- @EnableAutoConfiguration    : 开启自动配置将Bean加载到SpringBoot。
- @SpringBootConfiguration    : 用于加载配置类。
- @SpringBootApplication : 加在SpringBoot项目启动类上,实际等于@ComponentScan + @EnableAutoConfiguration + @SpringBootConfiguration 三个注解



