### SpringCloud
如何写通用的 interface ，其他项目可以使用。

feign 调用

服务端：
```java
@RestController
public class HelloController {
	
    @RequestMapping("/hello")
    public String index(@RequestParam String name) {
        return "hello "+name+"，this is first messge";
    }
}
```

调用端[消费端]: feign声明
```java
@FeignClient(name= "spring-cloud-producer")
public interface HelloRemote {
    @RequestMapping(value = "/hello")
    public String hello(@RequestParam(value = "name") String name);
}
```

断路由：
```java
@RestController
@RequestMapping("/api/order")
public class OrderResource {
  @HystrixCommand(fallbackMethod = "createFallback")
  @PostMapping(value = "/")
  public Order buyTicket(@RequestBody Order order) {
    return orderService.create(order);
  }
  private Order createFallback(Order order) {
    return orderService.createFallback(order);
  }
}
```

~~~
Feign对Hystrix的支持

虽然Feign和Hystrix是两个独立的功能模块，但是只要在项目依赖里面包含Hystrix的库，那么Feign就会自动使用Hystrix来封装相应的调用方法。

@FeignClient标签中也包含fallback的配置。
它跟Hystrix的fallback配置不同，Hystrix的fallback配置是配置一个方法为出错时的调用方式，而@FeignClient里面的fallback配置的是一个类，这个类必须继承这个FeignClient的接口。

~~~


事务和熔断注意：
~~~
HystrixCommand和Transactional公用

一般情况下，在Spring中，一个方法使用@Transactional标签后，方法内出现任何错误，都会数据库的操作都会回退，但是，如果把它和@HystrixCommand公用就会不一样了。

我们知道，Spring使用代理模式实现添加了事务标签的方法，也就是在这个方法调用的前后添加事务控制代码，通过try/catch实现出错的时候回退操作。

但是，如果你同时使用了@HystrixCommand标签，它也会通过代理把方法内容封装一下，来实现监控运行情况，和实现断路器功能等。

而且默认会在独立的线程里面执行方法，这样，就跟外面的启用的事务不在一个线程里，所以事务就不会起作用。

对于这个，一个简单的办法就是把这两个标签拆开，写到2个service类里，由于@HystrixCommand是在事务里面执行，那就先调用hystrix的：
~~~
```

@Service
public class ServiceA {
    @Inject ServiceAWithTransaction serviceTrans;
    @HystrixCommand
    public void callMyMethod() {
        serviceTrans.callMyMethod();
    }
}
```
~~~
然后在另一个service里面用事务
~~~
```
@Service
public class ServiceAWithTransaction {
    @Transactional
    public void callMyMethod() {
        // ....
    }
}
```

```
https://start.spring.io/
```

自我保护模式：
```
自我保护机制的工作机制是如果在15分钟内超过85%的客户端节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，Eureka Server自动进入自我保护机制，此时会出现以下几种情况：

1、Eureka Server不再从注册列表中移除因为长时间没收到心跳而应该过期的服务。
2、Eureka Server仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上，保证当前节点依然可用。
3、当网络稳定时，当前Eureka Server新的注册信息会被同步到其它节点中。

开发模式的时候 可以关闭，防止时间一长就进入自我保护模式，生产环境需要开启，默认是开启的。
```

[spring cloud feign](http://projects.spring.io/spring-cloud/spring-cloud.html#spring-cloud-feign-inheritance)



[技术文章-网易乐得技术团队 Spring Cloud技术分析](http://tech.lede.com/2017/03/15/rd/server/SpringCloud1/)

[技术文章-纯洁的笑 Spring Cloud系列文章](http://www.ityouknow.com/spring-cloud)

[技术文章- 程序猿DD Spring Cloud基础教程](http://blog.didispace.com/Spring-Cloud%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/)

[重点 Spring cloud 中文手册](https://springcloud.cc/spring-cloud-dalston.html)

[REST微服务的分布式事务实现-使用Spring Cloud的fallback模式](http://codin.im/2017/06/02/rest-micro-services-distributed-trasaction-3-fallback/)

[强力推荐--REST微服务的分布式事务实现-分布式系统、事务以及JTA介绍
](http://codin.im/2017/05/14/rest-micro-services-distributed-trasaction-1-jta/)

[Spring Cloud中，Eureka常见问题总结](http://www.itmuch.com/spring-cloud-sum-eureka/)

[Eureka Clustering documentation and best practices](https://github.com/spring-cloud/spring-cloud-netflix/issues/203)

[SpringCloud Eureka自我保护机制](http://cdn2.jianshu.io/p/ee4785a212f6)

[探讨通过Feign配合Hystrix进行调用时异常的处理](http://blog.didispace.com/rencong-1/)     


[***Spring Cloud系列-微服务篇(三) Eureka高可用集群 (1)](http://alanzh.com/archives/52/)  