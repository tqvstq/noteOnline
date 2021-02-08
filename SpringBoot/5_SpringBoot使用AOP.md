# SpringBoot使用AOP

### POM文件引入依赖
```xml
<!-- SpringBoot AOP依赖 -->
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
``` 

### properties文件配置 
一般来说只要我们加入了AOP依赖,就会自动触发 AOP 的关联行为,还可以通过配置来干预
```properties
# 是否开启AOP
spring.aop.auto=true
# 启用针对类的代理 而不是接口的代理
spring.aop.proxy-target-class=false
``` 

### 实现 AOP 切面
在新创建的类上加上两个注解即可,`@Aspect`: 表示为一个切面类，定义切面类的时候需要打上这个注解。`@Component` :注解让该类交给 Spring 来管理。

#### AOP常用注解
- `@Pointcut`: 定义一个切面，即某件事情的切入点的表达式。
- `@Before`: 前置通知,在做某件事之前做的事。
- `@After`: 后置通知,在做某件事之后做的事。
- `@AfterReturning`: 返回后通知,在执行结束之前执行,如果异常将不执行,通常用于对其返回值做增强处理。
- `@AfterThrowing`: 异常通知,在做某件事抛出异常时，执行。
- `@Around`: 环绕通知,在做某件事前后都执行，异常后不执行后面。

#### AOP切入点表达式 
 [参考地址]( https://www.cnblogs.com/ityangshuai/p/11923696.html )
1. execution：用于匹配子表达式。(常用)
> execution() 为表达式主体
  第一个 * 号的位置：表示返回值类型，* 表示所有类型
  包名：表示需要拦截的包名，后面的两个句点表示当前包和当前包的所有子包,
  第二个 * 号的位置：表示类名，* 表示所有类
  *(..) ：这个星号表示方法名，* 表示所有的方法，后面括弧里面表示方法的参数，两个句点表示任何参数
```java
//匹配com.cjm.model包及其子包中所有类中的所有方法，返回类型任意，方法参数任意
@Pointcut("execution(* com.cjm.model..*.*(..))")
public void before(){}
```
2. @annotation ：匹配连接点被它参数指定的Annotation注解的方法。也就是说，所有被指定注解标注的方法都将匹配。(常用)
> annotation() 方式是针对某个注解来定义切面，比如我们对具有@GetMapping注解的方法做切面，可以如下定义切面
```java
@Pointcut("@annotation(org.springframework.web.bind.annotation.GetMapping)")
public void before(){}
```
3. within：用于匹配连接点所在的Java类或者包。
```java
//匹配Person类中的所有方法
@Pointcut("within(com.cjm.model.Person)")
public void before(){}
//匹配com.cjm包及其子包中所有类中的所有方法
@Pointcut("within(com.cjm..*)")
public void before(){}
```
4. this：用于向通知方法中传入代理对象的引用。
```java
@Before("before() && this(proxy)")
public void beforeAdvide(JoinPoint point, Object proxy){
      //处理逻辑
}
```
5. target：用于向通知方法中传入目标对象的引用。
```java
@Before("before() && target(target)
public void beforeAdvide(JoinPoint point, Object proxy){
      //处理逻辑
}
```
6. args：用于将参数传入到通知方法中。
```java
 @Before("before() && args(age,username)")
public void beforeAdvide(JoinPoint point, int age, String username){
      //处理逻辑
}
```
7. @within ：用于匹配在类一级使用了参数确定的注解的类，其所有方法都将被匹配。 
```java
// 所有被@AdviceAnnotation标注的类都将匹配
@Pointcut("@within(com.cjm.annotation.AdviceAnnotation)") 
public void before(){}
```
8. @target ：和@within的功能类似，但必须要指定注解接口的保留策略为RUNTIME。
```java
@Pointcut("@target(com.cjm.annotation.AdviceAnnotation)")
public void before(){}
```
9. @args ：传入连接点的对象对应的Java类必须被@args指定的Annotation注解标注。
```java
@Before("@args(com.cjm.annotation.AdviceAnnotation)")
public void beforeAdvide(JoinPoint point){
      //处理逻辑
}　
```
10. bean：通过受管Bean的名字来限定连接点所在的Bean。该关键词是Spring2.5新增的。
```java
@Pointcut("bean(person)")
public void before(){}
```

#### 自定义注解 + AOP实现操作日志记录

##### 编写自定注解
```java
/**
 * 日志记录、自定义注解
 *
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface BussinessLog {

    /**
     * 业务名称
     *
     * @return
     */
    String value() default "";

    /**
     * 用户行为
     *
     * @return
     */
    EBehavior behavior();

    /**
     * 是否将当前日志记录到数据库中
     */
    boolean save() default true;
}
``` 

##### 用户行为使用枚举类方便扩展
```java
/**
 * 用户行为枚举类
 */
public enum EBehavior {
    BLOG_TAG("点击标签", "blog_tag"),
    BLOG_SORT("点击博客分类", "blog_sort"),
    BLOG_CONTNET("点击博客", "blog_content"),
    BLOG_PRAISE("点赞", "blog_praise"),
    FRIENDSHIP_LINK("点击友情链接", "friendship_link"),
    BLOG_SEARCH("点击搜索", "blog_search"),
    STUDY_VIDEO("点击学习视频", "study_video"),
    VISIT_PAGE("访问页面", "visit_page"),
    VISIT_SORT("点击归档", "visit_sort"),
    BLOG_AUTHOR("点击作者", "blog_author"),
    PUBLISH_COMMENT("发表评论", "publish_comment"),
    DELETE_COMMENT("删除评论", "delete_comment"),
    REPORT_COMMENT("举报评论", "report_comment"),
    VISIT_CLASSIFY("点击分类", "visit_classify");
    
    private String content;
    private String behavior;

    private EBehavior(String content, String behavior) {
        this.content = content;
        this.behavior = behavior;
    }
}
``` 
##### 编写AOP代码
```java
/**
 * 日志切面
 */
@Aspect
@Component
@Slf4j
public class LoggerAspect {

    private SysLog sysLog;

    private ExceptionLog exceptionLog;

    @Autowired
    private WebVisitService webVisitService;
    @Pointcut(value = "@annotation(bussinessLog)")
    public void pointcut(BussinessLog bussinessLog) {

    }

    @Around(value = "pointcut(bussinessLog)")
    public Object doAround(ProceedingJoinPoint joinPoint, BussinessLog bussinessLog) throws Throwable {

        //先执行业务
        Object result = joinPoint.proceed();

        try {
            // 日志收集
            handle(joinPoint);
        } catch (Exception e) {
            log.error("日志记录出错!", e);
        }

        return result;
    }

    private void handle(ProceedingJoinPoint point) throws Exception {

        HttpServletRequest request = RequestHolder.getRequest();

        Method currentMethod = AspectUtil.INSTANCE.getMethod(point);
        //获取操作名称
        BussinessLog annotation = currentMethod.getAnnotation(BussinessLog.class);

        boolean save = annotation.save();

        EBehavior behavior = annotation.behavior();

        String bussinessName = AspectUtil.INSTANCE.parseParams(point.getArgs(), annotation.value());

        String ua = RequestUtil.getUa();

        log.info("{} | {} - {} {} - {}", bussinessName, IpUtils.getIpAddr(request), RequestUtil.getMethod(), RequestUtil.getRequestUrl(), ua);
        if (!save) {
            return;
        }

        // 获取参数名称和值
        Map<String, Object> nameAndArgsMap = AopUtils.getFieldsName(point);

        Map<String, String> result = EBehavior.getModuleAndOtherData(behavior, nameAndArgsMap, bussinessName);

        AopUtils.getFieldsName(point);

        if (result != null) {
            String userUid = "";
            if (request.getAttribute(SysConf.USER_UID) != null) {
                userUid = request.getAttribute(SysConf.USER_UID).toString();
            }
            webVisitService.addWebVisit(userUid, request, behavior.getBehavior(), result.get(SysConf.MODULE_UID), result.get(SysConf.OTHER_DATA));
        }
    }
}

// 这里使用了一个AspectUtils工具类
/**
 * AOP相关的工具
 */
public enum AspectUtil {

    INSTANCE;

    /**
     * 获取以类路径为前缀的键
     *
     * @param point 当前切面执行的方法
     */
    public String getKey(JoinPoint point, String prefix) {
        String keyPrefix = "";
        if (!StringUtils.isEmpty(prefix)) {
            keyPrefix += prefix;
        }
        keyPrefix += getClassName(point);
        return keyPrefix;
    }

    /**
     * 获取当前切面执行的方法所在的class
     *
     * @param point 当前切面执行的方法
     */
    public String getClassName(JoinPoint point) {
        return point.getTarget().getClass().getName().replaceAll("\\.", "_");
    }

    /**
     * 获取当前切面执行的方法的方法名
     *
     * @param point 当前切面执行的方法
     */
    public Method getMethod(JoinPoint point) throws NoSuchMethodException {
        Signature sig = point.getSignature();
        MethodSignature msig = (MethodSignature) sig;
        Object target = point.getTarget();
        return target.getClass().getMethod(msig.getName(), msig.getParameterTypes());
    }

    public String parseParams(Object[] params, String bussinessName) {
        if (bussinessName.contains("{") && bussinessName.contains("}")) {
            List<String> result = RegexUtils.match(bussinessName, "(?<=\\{)(\\d+)");
            for (String s : result) {
                int index = Integer.parseInt(s);
                bussinessName = bussinessName.replaceAll("\\{" + index + "}", JSON.toJSONString(params[index - 1]));
            }
        }
        return bussinessName;
    }
}

// AOPUtils 工具类
/**
 * 切面相关工具类
 */
@Slf4j
public class AopUtils {

    /**
     * 获取参数名和值
     * @param joinPoint
     * @return
     */
    public static Map getFieldsName(ProceedingJoinPoint joinPoint) throws ClassNotFoundException, NoSuchMethodException {
        // 参数值
        Object[] args = joinPoint.getArgs();

        Signature signature = joinPoint.getSignature();
        MethodSignature methodSignature = (MethodSignature) signature;
        String[] parameterNames = methodSignature.getParameterNames();

        // 通过map封装参数和参数值
        HashMap<String, Object> paramMap = new HashMap();
        for (int i = 0; i < parameterNames.length; i++) {
            paramMap.put(parameterNames[i], args[i]);
        }
        return paramMap;
    }
}
``` 
##### 异步收集日志
```java
// 这里日志收集采用异步方法
// 在Spring中，基于@Async标注的方法，称之为异步方法；这些方法将在执行的时候，将会在独立的线程中被执行，调用者无需等待它的完成，即可继续其他的操作。
    @Async
    @Override
    public void addWebVisit(String userUid, HttpServletRequest request, String behavior, String moduleUid, String otherData) {

        //增加记录（可以考虑使用AOP）
        Map<String, String> map = IpUtils.getOsAndBrowserInfo(request);
        String os = map.get("OS");
        String browser = map.get("BROWSER");
        WebVisit webVisit = new WebVisit();
        String ip = IpUtils.getIpAddr(request);
        webVisit.setIp(ip);

        //从Redis中获取IP来源
        String jsonResult = stringRedisTemplate.opsForValue().get("IP_SOURCE:" + ip);
        if (StringUtils.isEmpty(jsonResult)) {
            String addresses = IpUtils.getAddresses("ip=" + ip, "utf-8");
            if (StringUtils.isNotEmpty(addresses)) {
                webVisit.setIpSource(addresses);
                stringRedisTemplate.opsForValue().set("IP_SOURCE" + BaseSysConf.REDIS_SEGMENTATION + ip, addresses, 24, TimeUnit.HOURS);
            }
        } else {
            webVisit.setIpSource(jsonResult);
        }
        webVisit.setOs(os);
        webVisit.setBrowser(browser);
        webVisit.setUserUid(userUid);
        webVisit.setBehavior(behavior);
        webVisit.setModuleUid(moduleUid);
        webVisit.setOtherData(otherData);
        webVisit.insert();
    }
``` 
##### 使用自定注解标识收集位置
```java
    @BussinessLog(value = "发表评论", behavior = EBehavior.PUBLISH_COMMENT)
    @ApiOperation(value = "增加评论", notes = "增加评论")
    @PostMapping("/add")
    public String add(@Validated({Insert.class}) @RequestBody CommentVO commentVO, BindingResult result) {

        QueryWrapper<WebConfig> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq(SysConf.STATUS, EStatus.ENABLE);
        WebConfig webConfig = webConfigService.getOne(queryWrapper);
        if (SysConf.CAN_NOT_COMMENT.equals(webConfig.getStartComment())) {
            return ResultUtil.result(SysConf.ERROR, MessageConf.NO_COMMENTS_OPEN);
        }
        ThrowableUtils.checkParamArgument(result);

        if (commentVO.getContent().length() > SysConf.TWO_TWO_FIVE) {
            return ResultUtil.result(SysConf.ERROR, MessageConf.COMMENT_CAN_NOT_MORE_THAN_225);
        }
        Comment comment = new Comment();
        comment.setSource(commentVO.getSource());
        comment.setBlogUid(commentVO.getBlogUid());
        comment.setContent(commentVO.getContent());
        comment.setUserUid(commentVO.getUserUid());
        comment.setToUid(commentVO.getToUid());
        comment.setToUserUid(commentVO.getToUserUid());
        comment.setStatus(EStatus.ENABLE);
        comment.insert();

        User user = userService.getById(commentVO.getUserUid());

        //获取图片
        if (StringUtils.isNotEmpty(user.getAvatar())) {
            String pictureList = this.pictureFeignClient.getPicture(user.getAvatar(), SysConf.FILE_SEGMENTATION);
            if (webUtils.getPicture(pictureList).size() > 0) {
                user.setPhotoUrl(webUtils.getPicture(pictureList).get(0));
            }
        }
        comment.setUser(user);

        return ResultUtil.result(SysConf.SUCCESS, comment);
    }
``` 

##### 完成啦