## 对AOP的理解

系统是由许多不同的组件所组成的，每一个组件各负责一块特定功能。除了实现自身核心功能之外，这些组件还经常承担着额外的职能。例如日志事务管理和安全这样的的核心业务经常融入到自身具有核心业务逻辑的组件中去。这些系统服务经常被称为横切关注点，因为它们会跨越系统的多个组件。

AOP：将程序中的交叉业务逻辑，封装成一个切面然后注入到目标对象中区。

### 对IOC的理解

容器概念、控制反转、依赖注入

IOC容器：实际上就是一个map（key，value），里面存着各种对象。

### BeanFactory和ApplicationContext有什么区别

ApplicationContext是BeanFactory的子接口

ApplicationContext功能更完备

ApplicationContext可以载入多个（有继承关系）上下文，使得每一个上下文都专注于一个特定的层次，比如应用的web层。

BeanFactory采用延迟加载来注入bean，只有在调用bean的时候才能

### Spring Bean 的生命周期

解析类得到BeanDefinition

如果有多个构造方法，则要推断构造方法

确定好构造方法后，进行实例化得到一个对象

对对象中加了@Autowired注解的属性进行填充

回调Aware方法，比如BeanNameAware，BeanFactoryAware

调用BeanPostProcessor的初始化前的方法

调用初始化方法

调用BeanPostProcessor的初始化后的方法，在这里会进行AOP

如果当前创建的bean是单例的则会把bean放入单例池

使用bean

spring容器关闭时调用DisposableBean的destory方法

## Spring支持的几种Bean的作用域

singleton：默认，每一个容器中只有一个bean的实例，单例模式有BeanFactory自身来维护，生命周期与springIOC容器一致。

prototype：为每一个bean请求提供一个实例。每次注入式都会创建一个新的对象。

request：bean被定义为在每一个http请求中创建的单例对象，单个请求中都会复用这一个单例对象。

session：与request范围类似，确保每一个session中有一个bean的实例，在session过期后bean会随之失效。

application：bean被定义为ServletContext的生命周期中复用到一个单例对象。

websocket：bean被定义为在websocket的生命周期中复用一个单例对象。

## Spring框架中的单例Bean是线程安全的吗？

不是线程安全的。

Spring 中的Bean默认是单例模式的，框架并没有对bean进行多线程封装处理。

如果bean是有状态的那就需要开发人员自己来进行线程安全的保证，把singleton改成prototype这样每次请求都新建一个bean就可以保证线程安全了。

## spring框架中使用了哪些设计模式

简单工厂：有一个工厂类根据闯入的参数，动态决定创建哪个产品类

> spring中的BeanFactory就是简单工厂模式的体现，更具传入唯一标识来获取Bean对象，但是否是传入参数后创建还是传入参数前创建这个要根据具体情况来定。

工厂方法：

> 实现了FactoryBean接口的bean是一类叫做factory的bean。spring会使用getBean（）调用获得该bean时，会自动调用bean的getobject()方法，所以返回的不是factory这个bean，而是这个bean.getObject()

适配器模式：

> spring 定义了一个适配接口，是的每一个Controller有一种对应的适配器实现类，让适配器替代controller执行相应的方法。这样在扩张controller时，只需要增加一个适配器类来完成SpringMVC的扩展。

装饰器模式：动态地给一个对象添加一些额外的职能。比生成子类更加灵活。

> 包名包含Wrapper或者Decorator的都用到了。

动态代理

> AOP

观察者模式

> Spring 监听器用使用到了

策略模式

> spring 在访问资源时提供了多种方式，大量使用Resource接口来访问底层资源。

## Spring事务的实现方式和隔离级别

在使用Spring框架时，可以有两种使用事务的方式，一种是编程式的，一种是申明式，@Transactional注解就是申明式的 。

Spring是在数据库中的事务进行了扩展，以及提供一些方便程序员操作事务的方式。

一个方法在添加@Transactional注解后，spring会基于这个类生产的代理对象，将代理对象作为bean，当在使用这个代理对象的方法时，如果这个方法上存在@Transactional注解，那么代理逻辑会先把事务的自动提交设置为false，然后去执行原本的业务逻辑方法，如果没有异常进行事务提交，否则回滚。

异常回滚是可以配置的，默认是RuntimeException和Error进行回滚。

spring事务格力级别就是数据库隔离级别：

- read uncommitted（未提交读）
- read committed(提交读、不可重复读)
- repeatable read（可重复读）
- serializable（可串行化）

> 如果数据库的隔离级别和spring设置的隔离级别不同，以spring为准，不存在spring配置的级别才看数据库。

## spring事务传播机制

多个事务方法相互调用时，事务如何在这些方法间进行传播。

> 方法A是一个事务方法，方法A执行过程中调用了方法B，那么方法B有无事务以及B对事务的要求不同都会对方法A的事务具体执行造成影响，同事方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。

REQUIRED（Spring默认事务传播机制）：如果当前没有事务，则自己新建一个事务，如何当前存在事务，则加入这个事务

SUPPORTS：当前存在事务，则加入事务，如果没有事务，就以非事务方法执行

MANDATORY：当前存在事务，则加入事务，如果当前事务不存在，则异常

REQUIRES_NEW：创建一个新事务，如果存在当前事务，则挂起该事务

NOT_SUPPORTED：以非事务方式执行，如果当前存在事务，则挂起当前事务

NEVER：不使用事务，如果存在事务，则异常

NESTED：如果当前事务存在，则在嵌套事务中执行，否则REQUIRED的操作一样（开启一个事务）

## spring事务什么时候会失效

spring事务的原理是AOP，进行切面增强，则失效的原因是这个AOP失效了，常见情况如下几种：

1. 发生自调用，类里面使用this调用本类的方法（this通常省略），这是在this对象不是代理类，而是UserService对象本身。

2. 方法不是public的

   > @Transactional 只能用于public的方法上，佛则事务不会生效，如果要用在非public方法上，可以开启AspectJ代理模式。

3. 数据库不支持事务

4. 没有被spring管理

5. 异常被吃掉，事务不会回滚（或抛出的异常没有被定义）

## 什么是bean的自动装配，有哪些方式

开启自动装配，只需要在xml配置文件`<bean>`中定义“autowire”属性。

autowire属性的五种方式：

- no - 缺省情况下，自动配置是通过“ref”属性手动装配。
- byName - 根据bean的属性名称进行自动装配。
- byType - 根据bean的类型进行自动装配。
- constructor - 类似byType，不过是应用于构造器的参数。如果一个bean与构造器参数的类型形同，则自动装配否则异常。
- autodetect - 如果有默认构造器则通过constructor 进行装配，否则使用byType 进行装配

@Autowired自动装配bean，可以用于字段setter、构造方法上使用。

## Spring Boot、Spring MVC、Spring有什么区别

Spring是一个IOC容器用来管理bean，可以方便整合框架，提供Aop机制弥补oop的代码重复问题。

SpringMVC是基于spring的一个web框架

springboot是spring提供的一个快速开发工具包

## SpringMVC工作流程

1. 用户发送请求到DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping处理映射器
3. 映射器根据XML、注解进行查找，生成处理器以及处理器拦截器一并返回给DispatcherServlet
4. DispatcherServlet调用HandlerAdapter处理器适配器
5. HandlerAdapter处理适配调用具体的Controller
6. Controller执行返回ModelAndView
7. HandlerAdapter将ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover返回具体的view
10. DispatcherServlet装填数据
11. DispatcherServlet响应用户

## SpringMVC的主要组件

1. HandlerMapping
2. HandlerAdapter
3. HandlerExceptionResolver
4. ViewResolver
5. RequestTo
6. LocaleResolver
7. ThemeResolver
8. MultipartResolver
9. FlashMapManager

## SpringBoot自动配置原理



### @SpringBootApplication

#### 	@SpringBootConfiguration

​			*@Configuration*

#### 	@EnableAutoConfiguration

​			**@Import** AutoConfigurationImportSelector.class

​				@selectImports  通过SpringFactoriesLoader.loadFactoryNames加载META-INF/spring.factories中的EnableAutoConfiguration

​					@Configuration + @Bean加载配置完成自动装配

​			**@AutoConfigurationPackage**

​				@Import   AutoConfigurationPackages.Registrar.class

​					ImportBeanDefinitionRegistrar 注册扫描路径到全局变量，提供查询

#### 	@componentScan 用来定义扫描路径

## 如何理解Spring boot的Starter



使用Spring+SpringMVC使用，如果需要引入mybatis等框架，需要到xml中定义mybatis需要的beanstarter就是定义一个starter的jar包，写入@Configuration配置、将这些bean定义在里面，然后在starter包的META/spring.factories中写入该配置类，在springboot会按照约定加载改配置类

## SpringCloud和Dubbo的区别

底层协议：springcloud基于http协议，dubbo使用tcp协议，决定dubbo的性能更好。

注册中心：Springcloud使用Eureka或nacos，dubbo使用zookeeper

模型定义：springcloud则是将一个应用定义为一个服务，dubbo将一个借口定义为一个服务

springcloud是一个生态，而dubbo是springcloud生态中关于服务调用一种解决方案（服务治理）。

## springcloud核心组件以及作用



![image-20210907161906457](.\img\image-20210907161906457.png)

Eureka：服务注册与发现

注册：每个服务向Eureka登记元数据（ip地址、端口、版本号、通信协议等）

Eureka将各个服务维护在一个服务清单中（双层Map，第一层key是服务名，第二层key是实例，value是服务地址加端口）。同时对服务维持心跳，剔除不可用服务，集群中还可以相互注册服务清单。

发现：Eureka注册的服务之间调用不需要指定服务地址，而是通过服务名向注册中心咨询，并获取所有服务实例清单（缓存到本地），然后实现服务的请求访问。

Ribbon：服务间发起请求时，基于Ribbon做负载均衡，从一个服务的多台机器中选择一台（被调用方的服务地址有多个），Ribbon也是通过发起http请求，来进行的调用，只不过是通过调用服务地址来实现的。虽然说Ribbon不用去具体请求服务实例的ip地址或者域名了，但是每调用一个接口还是要手动发起http请求

```java
@RestController
public class ConsumerController{
    @Autowired
    RestTemplate restTemplate;
    @GetMapping("/a")
    public String helloConsumer(){
        return restTemplate.getForEntity("http://e/index",String.class).getBody();
    }
}
```

feign:基于feign的动态代理机制，根据注解和选择的机器，拼接请求URL，简化服务间的调用，在Ribbon的基础上进行了进一步的封装。单独抽取了一个组件，就是Spring Cloud Feign。在引入Spring Cloud Feign后，我们只需要创建一个接口并用注解的方式来配置它，即可完成对服务提供方的接口绑定。

调用远程就像调用本地服务一样

```java
@RestController
public class UserController{
    @GetMapping("/getUser")
    public String getUser(){
        return "";
    }
}

@FeignClient(name = "user")//服务名
public interface UserClient{
    @GetMapping("/getUser")
    String getUser();
}

@RestController
public class TestController{
    @Resource
    UserClient userClient;
    
    @RequestMapping("/test")
    public String test(){
        String uer = userClient.getUser();
        return user;
    }
}
```

Hystrix:发送请求是通过Hystrix的线程池来走，不同的服务走不同的线程池，实现了不同服务调用的隔离，通过统计接口超时次数返回默认值，实现服务熔断和降级

## 介绍一下Spring，度过源码介绍一下大致流程

Spring是一个快速开发框架，Spring帮助程序员来管理对象



