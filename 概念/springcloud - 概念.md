### 一、Spring Cloud介绍

spring cloud是spring下子项目，springcloud是spring下的分布式服务模块。它将市面上一个非常好的一些技术整合到了一起，实现了很多分布式服务下的功能，配置管理，服务注册发现，服务路由，服务负载均衡，熔断器，等，常见的一些组件：eureka 注册中心，zull 网关， ribbon 负载均衡，feign声明式接口调用，hysix熔断功能。等非常丰富的插件组合

 

### 二、eureka注册中心

使用eureka当注册中心，然后将ip地址暴露给各大服务，

服务可以注册自己的ip 服务 到注册中心，

也可以从中心读取服务去调用，像eureka去订阅服务，eureka可以把服务对应的所有提供者地址发送给调用端，

中间使用心跳机制来刷新eureka与各个服务之间的联系

 

​     注册中心搭建过程：

导入eureka服务端依赖，spring-cloud-starter-netflix-eureka-server 然后在yml配置相关信息地址端口等，启动类上加入@EnableEurekaServer开启eureka注解 然后注册中心搭建好了

服务注册到注册中心：

服务需要注册到eureka中心时候，服务注册的项目导入eureka-client客户端依赖依赖，yml提供注册中心的ip地址，然后在启动类上使用EnableDiscoveryClient注解来运行注册的过程

从注册中心读取服务：

注册服务的同时也可以从eureka中心拿服务去调用， 可以在调用端使用resttemplate或者feign去读取服务调用，推荐feign调用，比较方便

 

### 三、robbin负载均衡

服务之间可以集群，服务在注册到eureka的时候，会规定一个serverNAME 我们可以启动多个服务实例，只要保持servername一致，会自动集群，这个时候，通过serverName来调用服务，无需通过服务端 IP地址调用，通过servername的方式调用，就可以运行ribbon功能，多次调用可以在服务之间，进行轮训调用，实现了负载均衡的效果，通过yml中NFLoadBalancerRuleClassName 还可以配置轮训档案策略

### 四、feign接口调用

feign是调用服务的一种方式，程序使用restTemplate调用太过繁琐，所以一般会使用feign声明式接口调用，我们以接口，方法的形式去调用服务，首先，项目中引入openfeign依赖，然后新建一个服务对应的接口，用@FeignClient注解去设置这个接口指向的servername。 在接口中写方法，方法用@*Mapping注解去规定这个方法调用的具体服务，返回值，参数等，都可以设置。 启动类中需要用@EnableFeignClients开启扫描client 然后完成client实例化，这个时候，在程序的service可以直接注入client接口直接调用服务了。

  注：fiegn已经整合了ribbon负载功能，直接yml种配置ribbon策略就行

 

 

### 五、hystrix熔断器

熔断器是在当调用服务的时候，如果服务端，出现了异常或者其他报错，无法正常响应返回数据的情况下一种保护程序正常运行的机制，当服务出现异常时，我们要对服务进行降级处理，而不能直接将错误返回给调用者，给个友好提示，这样可以提高用户体验。系统特别繁忙时，一些次要服务暂时中断，优先保证主要服务的畅通，一切资源优先让给主要服务来使用

项目中使用：

引入netflix-hystrix依赖，启动类上加上@EnableHystrix注解开启熔断器，我们一般使用feignclient来调用服务，feign默认整合了hustrix熔断功能，需要从yml中开启就可以， 然后创建一个熔断类，去实现client接口，重新client中所有方法，并在每个方法中实现如果这个方法服务调用出现异常，中的友好信息提示，在client接口中设置实现类为熔断类。那么在程序调用服务的时候，如果服务端没有问题，那么可以正常调用，当服务端无法正常提供服务的时候，会自动调用熔断类中的方法进行返回，这样就实现了熔断功能

### 六、zull网关

服务网关是微服务架构中一个不可或缺的部分。通过服务网关统一向外系统提供REST API的过程中，除了具备服务路由、均衡负载功能之外，它还具备了权限控制等功能。Spring Cloud Netflix中的Zuul就担任了这样的一个角色，为微服务架构提供了前门保护的作用，同时将权限控制这些较重的非业务逻辑内容迁移到服务路由层面，使得服务集群主体能够具备更高的可复用性和可测试性。我们把所有的服务都交由网关，这个时候，网关可以在服务访问钱进行，服务鉴权，跨域，权限，等一些判断，保证整个架构的安全

网关中的路由功能，可以整合所有的服务，然后统计提供给外端进行访问，当外部请求需要访问服务时，那么需要访问网关，然后通过网关路由到某个服务，不能直接访问服务，这样不够安全，同时会破坏微服务的设计原则，

项目中使用：网关项目中导入netflix-zuul依赖，yml中规定servername 配置eureka地址，启动类中加入@EnableZuulProxy注解，开启网关功能 导入eureka客户端依赖，@EnableDiscoveryClient注册链接到eureka中心，yml可配置，从eureka中拉取所有的服务，拉取完所有的服务，就可以配置路由信息了，例如规定，请求有xxx访问地址那么就分配路由到xxx服务项目中 给所有的service中配置访问路径就可以，然后在访问服务的时候，就必须先访问网关项目，然后携带对应的服务路径就可以了

网关的过滤器：

网关中海可以设置过滤器，就是说，默认拦截过滤所有请求，然后可以在过滤器中进行一些权限和鉴权判断，本次访问是否被允许，可以保障服务的安全线，如果 是非法请求，可以做拦截，不允许访问，不会进行路由分发

实现：自定义一个filter类，然后继承ZuulFilter过滤器，实现方法filterType 过滤器类型，filterOrder优先级，shouldFilter是否生效，run方法，在run方法写业务逻辑就可以，允许访问，正常返回，不允许访问，就获得response设置false和错误状态码就可以

 

 

 

 

 

 

 

### 七、dubbo和spring cloud有啥区别

首先，dubbo采用zookeeper做注册中心， springcloud采用eureka注册中心 eureka基于spring boot

Dubbo采用的拦截器机制，开发成本略为复杂，     springcloud相对简单效率

Dubbo采用rcp协议，效率高                      springcloud 采用 http协议，效率低于dubbo

### 八、微服务下分布式事务的控制

由于微服务是调用各个服务的形式，同时各个服务可能不同的业务模块，连接的数据库也不一致，例如，商品购买功能付款，需要付款扣款，和同时修改订单状态，订单服务，连接的是订单服是务数据库，个人余额是连接是用户数据库，这个时候，在一个service中调用两个服务，而两个服务又是跨库的存在所以，一旦发生先扣款成功，订单修改失败的情况下，如何保证扣款的数据库事务也回滚，这就是分布式事务，在多个数据库之间发生的事务，就是分布式事务，咱们事务，只能控制同一数据库下的事务，多个数据库之间发生错误，是不会回滚上个数据库的，因此不规范所以采用分布式服务控制，

可调用tcc-transaction，补偿性分布式事务框架，用@Compensable @Transactional规定方法

Propagation.REQUIRED 规定隔离级别,手动开启事务，通过确保所有服务都调用成功后，在手动提交

### 九、微服务下的服务轮训策略

使用负载均衡带来的好处很明显：

当集群里的1台或者多台服务器down的时候，剩余的没有down的服务器可以保证服务的继续使用使用了更多的机器

保证了机器的良性使用，不会由于某一高峰时刻导致系统cpu急剧上升
 负载均衡有好几种实现策略

Ribbon负载均衡策略： 

RandomRule随机选择一个server

RoundRobinRule 轮询选择server

RetryRule：对选定的负载均衡策略机上重试机制

WeightedResponseTimeRule：根据相应时间分配一个weight，相应时间越长，weight越小，被选中的可能性越低。

BestAvailableRule：选择一个最小的并发请求的server   

ZoneAvoidanceRule：复合判断server所在区域的性能和server的可用性选择server

AvailabilityFilteringRule：过滤掉失败的后端