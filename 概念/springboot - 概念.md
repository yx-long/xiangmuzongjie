### 一、springboot介绍，为什么要使用springboot

Spring Boot是一个技术栈，配置比较简单，依赖管理比较方便，解决了项目技术之间的兼容问题，可以更好的整合cloud微服务框架，以及市面上常用的主流技术。



## 二、为什么要学习SpringBoot

java的开发框架设计模式还是 比较麻烦的，各种配置，xml，等

- 复杂的配置，

  项目各种配置繁琐，导致开发效率不高，xml较多，每个技术都要交由spring管理，增加额外的xml文件

- 依赖混乱不透明。

  项目中一般都会引入大量的依赖，因为技术比较多，当引入技术比较多了，还需要考虑技术的版本是否与spring对应，与其他技术间是否冲突，如果出现了依赖版本冲突，解决起来非常麻烦，项目之间技术的兼容性是一个棘手的事情



而SpringBoot解决了，繁琐配置，以及依赖混乱的局面

> Spring Boot 简化了基于Spring的应用开发，只需要“run”就能创建一个独立的、生产级别的Spring应用。Spring Boot为Spring平台及第三方库提供开箱即用的设置（提供默认设置，存放默认配置的包就是启动器），这样我们就可以简单的开始。多数Spring Boot应用只需要很少的Spring配置。

我们可以使用SpringBoot创建java应用，并使用java –jar 启动它，就能得到一个生产级别的web工程。

## 三、SpringBoot的特点

Spring Boot 主要目标是：

- 为所有 Spring 的开发者提供一个非常快速的、广泛接受的入门体验
- 开箱即用（启动器starter-其实就是SpringBoot提供的一个jar包），但通过自己设置参数（.properties/yml），即可快速摆脱这种方式。
- 提供了一些大型项目中常见的非功能性特性，如内嵌服务器、安全、指标，健康检测、外部化配置等
- 绝对没有代码生成，也无需 XML 配置。

### 四、Spring boot 启动方式

打包命令(mvn package)， 启动命令(java -jar xx.jar)运行，

用 Maven插件运行(mvn spring-boot:run) ，

直接执行 main 方法运行



### 五、Spring boot 注解：

@SpringBootApplication用在启动类上，用于启动加载默认的配置，读取yml文件

包含 @SpringBootConfiguration：标明当前类是一个springboot配置类（只能有一个）组合了 @Configuration 注解，标明当前类是一个配置类（可有多个）

@EnableAutoConfiguration：自动完成默认配置（从start启动器中）

@ComponentScan：扫描包默认当下包及其子包，完成类的创建 

@RestController :标志为控制层类，默认本类所有方法返回json 底层是 controller+responsebody结合

@GetMapping 限制该方法为 get请求 用户查询

@PostMapping 限制该方法为post请求 用于新增

@PutMapping 限制该方法为 put请求 用于修改

@DeletetMapping 限制该方法为 delete请求 用户删除

 

@RequestBody 接受前台json格式的参数 转为实体类

@Responsebody 将后台的返回值转为json格式输出到前台





### 六、Springboot 自动配置原理

Boot项目启动类配置SpringBootApplication注解。这个注解底层包含了@SpringBootConfiguration（ioc容器配置类），@EnableAutoConfiguration自动化配置类@ComponentScan（注解扫描类，实例化相关对象）当启动类执行后，启动类的注解，会默认扫描所有的类完成创建，这个时候，会扫描到spring-boot-autoconfigure这个jar包依赖，这个依赖中定义了大量技术的配置类，mvc，缓存，对列，mybatis应有尽有，每个类中都定义了Configuration注解，注解完成了各个类的创建，而且属性，都是从application配置文件中读取，并且进入属性注入的，所以，启动类启动后，扫描创建默认的配置类，然后配置类去检查application中有没有配置该技术，有的话。直接读取创建，完成了自动配置



### 七、Springboot拦截器

拦截器是每个控制层框架的必备功能，boot也不例外，首先，创建一个自定义拦截器类，然后实现HandlerInterceptor接口，会重写三个方法preHandle postHandle afterCompletion，然后在创建一个configuration配置类 实现WebMvcConfigurer接口，重写addInterceptors 增加拦截器方法，把我们自定义的拦截器类创建出来，通过addInterceptors方法增加到InterceptorRegistry就可以。同时也需要配置拦截的url规则



### 八、springboot搭建使用过程

项目中需要引入boot父包依赖spring-boot-starter-parent ，以及web启动器依赖，只要规定了父包依赖版本，web依赖可根据父包版本自动匹配合适的对应版本，然后，项目配置启动类，boot是由启动类启动的，内嵌部署启动即可，配置好相应的配置后，就可以进行开发了，springboot一般用于前后端分离的开发模式，后端提供接口服务，返回json数据就可以，我们一般新建跟业务相关的controller然后用@restController标明为控制层框架，然后在框架中正常Autowire注入service或者其他服务类，然后使用@getMapping或者post put delete来完成不容的服务访问方式，访问状态等，返回值一般都用ResponseEntity 类，可以设置返回的状态码，同时项目中还需要异常声明方式要需要使用自定义异常，因为采用rest风格开发，所以用默认异常机制，返回不太友好，开发好接口之后，开发人员一般先完成自测，使用postman，inso等软件完成测试，可以正常的返回数据，状态码，就开发成功了，

### 九、Restcontroller controller区别

RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。

 

1)如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver则不起作用，返回的内容就是Return 里的内容（String/JSON）。

2))如果使用@RestController注解Controller，需要返回到指定页面，则需要配置视图解析器InternalResourceViewResolver，可以利用ModelAndView返回试图。

3)如果使用@Controller注解Controller，如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。

 

Controller注解源码上只有@Documented，@Component 文档和实例化注解

RestController注解上，有@Documented，@Component 之外的@responseBody注解

### 十 连接池的作用

 一个普通的Java程序，要查询数据库的数据  每次访问数据库都要进行一次tcp连接和数据库认证等操作，而这些操作非常影响服务器的性能 ，连接池中放着数据库连接，应用服务需要的时候就去池子里面拿，用完之后归还给池子。数据库连接池负责分配、管理、释放数据库连接，它允许应用服务重复使用数据库连接，而非重新建立，数据库的连接创建和关闭均由连接池来实现。这样做的优点：减少网络开销，提升数据库性能