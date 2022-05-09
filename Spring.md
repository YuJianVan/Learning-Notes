### Spring常用注解

##### 组件扫描与配置类（扫描到有这些注解的对象纳入spring容器管理）

~~~java
@Component
@Controller
@Service
@Repository
~~~

~~~java
@ComponentScan // 定义一个启示扫描包的位置，扫描包下含有上面四个注解的内容
@Conditional   // 组件扫描时按条件装配到spring容器中||配置类的@Bean解析时按条件装配到spring容器中
@Configuration  // 将类作为配置类
@Bean   // 标注配置类中哪些方法作为defination定义
@Import  // 按类名导入类到容器中
@Lazy //标注在类上：延迟实例化和初始化(懒加载)  ||  标注在方法参数上/成员变量上，解决循环依赖的方法之一
@PropertySource  // 读取外部的properties文件，将键值对信息加入到environment环境中
~~~



##### 依赖注入相关

~~~java
@Autowired  // 添加在成员变量或者方法参数上
~~~



### SpringMVC常用注解：

##### mapping相关

~~~java
@RequestMapping  // 将请求路径与注解中的路径进行匹配，匹配成功则执行标有这个注解的方法完成请求。
// 也可加在类上，提取共同路径前缀
由@RequestMapping派生出来的注解：
    @GetMapping
    @PostMapping
    @PutMapping
    @DeleteMapping
    @PatchMapping
上述五个注解相当于在@RequestMapping(method=RequestMethod.XXX)加入限制条件
~~~



##### rest相关

~~~java
@RequestBody   // 处理请求体中的数据，将json数据转化成java对象
@ResponseBody  // 将java对象转化成json数据返回给响应体
@ResponseStatus  // 控制响应状态码
@RestController  // @ResponseBody和@Controller的组合注解
~~~



##### 统一处理

~~~java
@ControllerAdvice  // 增强Controller，放在类上可用于统一异常处理，MethodAttribute，转换器，统一数据预处理等
@ExceptionHandler  // 标注该注解的方法用于处理异常，在普通标有@Controller的类中为局部处理，在标有@ControllerAdvice的类中为全局处理。
@RestControllerAdvice  // @ResponseBody和@ControllerAdvice的组合注解

~~~



##### 参数

~~~java
@RequestHead   // 获取请求头中的参数信息。
@CookieValue   // 获取cookie中的值
@PathVariable  // 获取请求路径URI中的参数值(/xx/i)
@RequestParam  // 获取请求携带的参数(?后面的键值对)
~~~



##### scope(了解)

~~~java
@ApplicationScope  // 应用程序作用域
@RequestScope  // 请求作用域
@SessionScope  // 会话作用域
// 控制spring容器中标有上述注解的类的作用域

@ModelAttribute  
@RequestAttribute  
@SessionAttribute  
@SessionAttributes  
~~~



##### ajax

~~~java
@CrossOrigin  // 解决ajax请求的跨域问题
~~~



### SpringBoot

##### properties

~~~java
@EnableConfigurationProperties  // 启用@ConfigurationProperties功能(通过后处理器识别@ConfigurationProperties)
@ConfigurationProperties  // 将properties中的键值信息和java bean的属性进行绑定（初始化、赋值）
~~~



##### condition

~~~java
@ConditionalOnClass // 类路径下包含某类才条件成立进行装配
@ConditionalOnMissingBean // 缺失Bean时条件成立，将候补的bean装配到容器中
@ConditionalOnProperty  // properties文件中存在目标键值对，条件成立
~~~



##### auto

~~~java
@SpringBootApplication  // 由@EnableAutoConfiguration和@SpringBootConfiguration和@ComponentScan组合而成
@EnableAutoConfiguration  // 把自动配置类关联的bean注册到容器当中
@SpringBootConfiguration  // 表示springboot配置类
~~~

















![20200324164559660](C:\Users\86152\Desktop\20200324164559660.png)









<img src="C:\Users\86152\Desktop\20200324164333336.png" alt="20200324164333336" style="zoom: 200%;" />



