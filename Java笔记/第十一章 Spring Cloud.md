# 第十一章 Spring Cloud

Spring Cloud是一个成熟、完善、流行的微服务架构方案。被许多互联网大厂采用，例如阿里巴巴、腾讯、支付宝、网易、IBM、谷歌、京东、百度、滴滴等。

## 2. 1 Spring Boot

Spring Boot是一个Spring开发框架，设计初衷是简化Spring应用复杂的搭建及开发过程。该框架提供了一套简单的Spring模块依赖和管理工具，从而避免开发人员处理复杂的模块依赖和版本冲突问题，同时提供打包即可用的Web服务。

特点：

1. 快速创建独立的Spring应用程序。
2. 嵌入Tomcat和Undertow等Web容器，实现快速部署。
3. 自动配置JAR包依赖和版本控制，简化Maven配置。
4. 自动装配Spring实例，不需要XML配置。
5. 提供诸如性能指标、健康检查、外部配置等线上监控和配置功能。

### 2.1.2 Spring Boot Application Starters

​	Starts是一组资源依赖描述，用于为不同的Spring Boot应用提供一站式服务，而不必像传统的Spring项目那样，需要开发人员处理服务和服务之间的复杂依赖关系。

### 2.1.3 Spring Boot的常用组件及其使用

#### 1. Spring Boot使用MySQl

   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-jdbc</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      spring.datasource.url=jdbc:mysql://localhost/test#数据库地址
      spring.datasource.username=dbuser#数据库用户名
      spring.datasource.password=dbpass#数据库密码
      spring.datasource.driver-class-name=com.mysql.jdbc.Driver#数据库驱动
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private final JdbcTemplate jdbcTemplate;
          @Autowired
          public MyBean(JdbcTemplate jdbcTemplate) {
              this.jdbcTemplate = jdbcTemplate;
          }
      }
      ```

#### 2. Spring Boot使用Redis

   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-redis</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      spring.redis.database=0
      spring.redis.host=172.31.19.222
      spring.redis.port=6379
      spring.redis.password=
      spring.redis.pool.max-active=8
      spring.redis.pool.max-wait=-1
      spring.redis.pool.max-idle=8
      spring.redis.pool.mix-idle=0
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private final StringRedisTemplate template;
          @Autowired
          public MyBean(StringRedisTemplate template) {
              this.template = template;
          }
      }
      ```

#### 3. Spring Boot使用MongoDB

   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      spring.data.mongodb.uri=mongodb://user:secret@mongo1.example.com:12345,mongo2.example.com:23456/test
      #数据库的连接地址
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private final MongoTemplate template;
          @Autowired
          public MyBean(MongoTemplate template) {
              this.template = template;
          }
      }
      ```

      

#### 4. Spring Boot使用Neo4j
   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-neo4j</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      #Neo4j图数据库地址
      spring.data.neo4j.uri=bolt://my-server:7687
      #Neo4j图数据库用户名
      spring.data.neo4j.username=neo4j
      #Neo4j图数据库密码
      spring.data.neo4j.password=secret
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private final Session session;
          @Autowired
          public MyBean(Session session) {
              this.session = session;
          }
      }
      ```
#### 5. Spring Boot使用Solr
   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-solr</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      spring.data.solr.host: http://127.0.0.1:8080/solr/ciri_core
      #数据库的连接地址
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private SolrClient solr;
          @Autowired
          public MyBean(SolrClient solr) {
              this.solr = solr;
          }
      }
      ```
#### 6. Spring Boot使用ElasticSearch
   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      spring.data.elasticsearch.cluster-nodes=localhost:9300
      #数据库的连接地址
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private final ElasticsearchTemplate template;
          @Autowired
          public MyBean(ElasticsearchTemplate template) {
              this.template = template;
          }
      }
      ```
#### 7. Spring Boot使用Cassandra
   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      #Cassandra的命名空间
      spring.data.cassandra.keyspace-name=mykeyspace
      #Cassandra的数据库地址
      spring.data.cassandra.contact-points=cassandrahost1,cassandrahost2
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private CassandraTemplate template;
          @Autowired
          public MyBean(CassandraTemplate template) {
              this.template = template;
          }
      }
      ```
#### 8. Spring Boot使用RabbitMQ
   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-rabbitmq</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      #RabbitMQ服务地址
      spring.rabbit.host=localhost
      #RabbitMQ端口号
      spring.rabbit.port=5672
      #RabbitMQ用户名
      spring.rabbit.username=admin
      #RabbitMQ密码
      spring.rabbit.password=secret
      ```

   3. 使用服务组件

      ```java
      @Component
      public class MyBean {
          private final AmqpAdmin amqpAdmin;
          private final AmqpTemplate amqpTemplate;
          @Autowired
          public MyBean(AmqpAdmin amqpAdmin, AmqpTemplate amqpTemplate) {
              this.amqpAdmin = amqpAdmin;
              this.amqpTemplate = amqpTempalte;
          }
      }
      ```

4. 定义队列

   ```java
   @Configuration
   public class QueueConf {
       // 1：定义Queue实例对象，队列名称为someQueue
       @Bean(name = "message")
       public Queue queueMessage() {
           return new Queue("someQueue");
       }
   }
   ```

5. 发送消息

   ```java
   @Component
   public class MyBeanSender {
       @Autowired
       private AmqpTemplate template;
       public void send() {
           // 向消息队列someQueue发送一条hello, rabbit
           template.convertAndSend("someQueue", "hello, rabbit");
       }
   }
   ```

6. 接收消息

   ```java
   @Component
   public class MyBean {
       // 监听和接收队列someQueue上的消息
       @RabbitListener(queues = "someQueue")
       public void processMessage(String content) {
           
       }
   }
   ```

#### 9. Spring Boot使用Kafka
   1. 引入Starter

      ```xml
      <dependency>
      	<groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-kafka</artifactId>
      </dependency>
      ```

   2. 设置application.properties

      ```properties
      #Kafka服务地址
      spring.kafka.bootstrap-servers=localhost:9092
      #Kafka消费组
      spring.kafka.consumer.group-id=myGroup
      ```

   3. 发送消息

      ```java
      @Component
      public class MyBean {
          private final KafkaTemplate kafkaTemplate;
          @Autowired
          public MyBean(KafkaTemplate kafkaTemplate) {
              this.kafkaTemplate = kafkaTemplate;
          }
          public Response sendKafka() {
              // 向Kafka的someTopic发送一条消息
              kafkaTemplate.send("someTopic", "key", message);
          }
      }
      ```

   4. 接收消息

      ```java
      @Component
      public class MyBean {
          // 监听并接收someTopic上的消息
          @KafkaListener(topics = "someTopic")
          public void processMessage(String content) {
              System.out.println("message:" + content);
          }
      }
      ```

## 2.2 Spring Cloud Config

Spring Cloud Config为分布式系统提供统一的配置管理工具，应用程序在使用过程中可以像使用本地配置一样方便地添加、访问、修改配置中心的配置。

Spring Cloud Config将Environment的PropertySource抽象和配置中心的配置进行映射，以便应用程序可以在任何场景下获取和修改配置。

### 2.2.1 Spring Cloud Config 的原理

Spring Cloud Config支持将配置存储在配置中心的本地服务器、Git仓库或Subversion。在Spring Cloud Config的线上环境中，通常将配置文件集中放置在一个Git仓库里，然后通过配置中心服务端（Config Server）来管理所有的配置文件；

当某个服务实例需要添加或更新配置时，只要在该服务实例的本地将配置文件进行修改，然后推送到Git仓库，其他服务实例通过配置中心从Git服务端获取最新的配置信息。

为保证系统的稳定性，配置中心服务端可以进行多副本集群部署，前端使用负载均衡实现服务之间的请求转发。

### 2.2.2 Config Server 的定义与使用

（1）pom.xml添加依赖

（2）@EnableConfigServer添加

@EnableConfigServer开启Spring Boot项目对分布式配置中心的支持功能。

```java
@SpringBootApplication
@EnableConfigServer
public class BootApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootApplication.class, args);
    }
}
```

（3）applications.properties配置

```properties
#配置中心端口号
server.port=9000
#配置中心名称
spring.cloud.config.server.default-application-name=config-server
#配置Git仓库的地址
spring.cloud.config.server.git.uri=https://github.com/macroyuan/SpringCloud
#配置仓库的路径
spring.cloud.config.server.git.search-paths=SpringCloudConfig
#配置仓库的分支
spring.cloud.config.label=master
#访问Git仓库的用户名
spring.cloud.config.server.git.username=username
#访问Git仓库的用户密码(如果Git仓库为公开仓库，可以不填用户名和密码)
spring.cloud.config.server.git.password=password
```

（4）访问服务地址

启动应用程序，在浏览器地址栏输入http://localhost:9000/*/dev， 返回配置信息。

### 2.2.3 Config Client

（1）pom.xml添加依赖

（2）bootstrap.properties配置

bootstrap.properties中的spring.cloud.config.uri参数用于设置应用程序从哪个服务地址上获取配置信息，并可以通过spring.cloud.profile指定运行环境，例如开发环境（dev）、测试环境（test）、正式运行环境（pro）等。

（3）配置信息的使用

## 2.3 Spring Cloud Eureka

Eureka是Netflix公司微服务套件中的一部分，主要负责微服务架构中的服务治理。

服务治理必须实现服务注册和服务发现的功能。服务注册和发现指每个微服务向注册中心汇报自己的服务地址、服务内容、端口和服务状态等信息。当有服务依赖此服务时，从注册中心获取可用的服务列表并发起请求，服务消费者不用关心服务所处的位置和状态，具体用哪个实例对外提供服务则由注册中心决定。

![image-20210512161237648](C:\Users\MacroYuan\AppData\Roaming\Typora\typora-user-images\image-20210512161237648.png)

微服务的实体数量是动态的，可能是一台，也可能是两台、100台或1000台，微服务的数量是弹性的，灵活弹性根据客户端的压力来做弹性伸缩。客户端调微服务时，不是直接找微服务，而是先找注册中心，有哪些好的微服务，有哪些匹配的。客户端也可以按照各种负载均衡的策略去调i，结合一些负载均衡的算法来做一个灵活的调度。

### 2.3.1 Eureka的原理

Eureka实现了服务注册和服务发现的功能。Eureka角色分为注册中心（Eureka Server）和客户端（Eureka Client）。客户端指注册到注册中心的具体的服务实例，又可抽象分为服务提供者和服务消费者。服务提供者主要用于将自己的服务注册到服务中心供服务消费者使用，服务消费者从注册中心获取服务提供者对应的服务地址并调用该服务。

#### 1. 服务注册

在服务启动时，服务提供者会将自己的信息注册到Eureka Server，Eureka Server收到信息后，会将数据信息存储在一个双层结构的Map中，其中，第一层的Key是服务名，第二层的Key是具体服务的实例。

```java
//注册表数据结构InstanceInfo
private final ConcurrentHashMap<String, Map<String, Lease<InstanceInfo>>> registry = new ConcurrentHashMap<String, Map<String, Lease<InstanceInfo>>>();
//双层嵌套的HashMap
//第一层存储的Key为AppName应用名称（同一微服务节点可能又多个实例）
//第二层存储的Key为InstanceName实例名称
```

#### 2. 服务同步

在Eureka Server集群中，当一个服务提供者向其中一个Eureka Server注册服务后，该Eureka Server会向集群中的其他Eureka Server转发这个服务提供者的注册信息，从而实现Eureka Server之间的服务同步。

#### 3. 服务续约

当服务提供者在注册中心完成注册后，会维护一个续约请求来持续发送信息给该Eureka Server表示其正常运行，当Eureka Server长时间收不到续约请求时，会将该服务实例从服务列表中剔除。

#### 4. 服务启动

当一个Eureka Server初始化或重启时，本地注册服务为空。Eureka Server首先调用`syncUp()` 从别的节点获取所有的注册服务，然后执行Register操作，完成初始化，从而同步所有的服务信息。

#### 5. 服务下线

当服务实例正常关闭时，服务提供者会给注册中心发送一个服务下线的消息，注册中心收到消息后，会将该服务实例的状态置为下线，并将该服务下线的信息传播给其他服务实例。

#### 6. 服务发现

当一个服务实例依赖另一个服务时，这个服务实例作为服务消费者会发送一个信息给注册中心，请求获取注册的服务清单，注册中心会维护一份只读的服务清单返回给服务消费者。

#### 7.失效剔除

注册中心为每个服务设定一个服务失效时间。当服务提供者无法正常提供服务，而注册中心又没有收到服务下线的信息时，注册中心会创建一个定时任务，将超过一定时间而没有收到服务续约消息的服务实例从服务清单中剔除。失效时间可以通过`eureka.instance.leaseExpirationDurationInSeconds`进行配置，定期扫描时间可以通过`eureka.server.evictionIntervalTimerInMs`进行配置。

### 2.3.2 Eureka的使用

#### 1. 注册中心的定义

1. pom.xml添加依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-eureka-server</artifactId>
       <version>1.4.6.RELEASE</version>
   </dependency>
   ```

2. 通过@EnableEurekaServer注解开启对注册中心的支持。

   ```java
   @SpringBootApplication
   @EnableEurekaServer
   public class EurekaserverApplication {
       public static void main(String[] args) {
           SpringApplication.run(EurekaserverApplication.class, args);
       }
   }
   ```

3. application.properties配置

   ```properties
   #微服务名称
   spring.application.name=eureka-server
   #修改端口
   server.port=8001
   #服务实例名称
   eureka.instance.hostname=localhost
   #服务注册中心的地址，端口主要是控制注册中心的地址客户端程序要知道控制中心在哪才决定要不要给它查询
   eureka.client.serviceUrl.defaultZone=http://localhost:8000/eureka/
   #设置是否将自己注册到Eureka Server，默认为true
   eureka.client.register-with-eureka=false
   #设置是否从Eureka Server获取注册信息，默认为true
   eureka.client.fetch-registry=false
   spring.main.allow-bean-definition-overriding=true
   ```

4. 访问服务地址

   启动应用程序，在浏览器地址栏中输入http://localhost:8001访问注册中心。

#### 2. 服务提供者的定义

1. pom.xml添加依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-eureka</artifactId>
       <version>1.4.6.RELEASE</version>
   </dependency>
   ```

2. 通过@EnableEurekaServer注解开启对注册中心的支持。

   ```java
   @SpringBootApplication
   @EnableEurekaServer
   public class EurekaclientApplication {
       public static void main(String[] args) {
           SpringApplication.run(EurekaclientApplication.class, args);
       }
   }
   ```

3. application.properties配置

   ```properties
   #微服务名称
   spring.application.name=eureka-client
   #修改端口
   server.port=8002
   eureka.client.serviceUrl.defaultZone=http://localhost:8001/eureka/
   ```

4. 定义服务

   ```java
   @RestController
   public class DiscoveryController {
       @Autowired
       private DiscoveryClient discoveryClient;
       @Value("${server.port}")
       private String port;
       @GetMapping("/serviceProducer")
       public Map serviceProducer() {
           //1.服务提供者的信息
           String services = "Services:" + discoveryClient.getServices() + " port :" + port;
           //2.服务提供者返回数据
           Map result = new HashMap();
           result.put("serviceProducer", services);
           result.put("time", System.currentTimeMillis());
           return result;
       }
   }
   ```

   

5. 调用服务

   在浏览器地址栏中输入http://localhost:8002/serviceProducer访问服务。

#### 3. 服务消费者的定义

1. pom.xml添加依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-eureka</artifactId>
       <version>1.4.6.RELEASE</version>
   </dependency>
   <dependency>
   	<groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-feign</artifactId>
       <version>1.4.6.RELEASE</version>
   </dependency>
   ```

2. 通过@EnableEurekaServer注解开启对注册中心的支持。

   ```java
   @SpringBootApplication
   @EnableEurekaServer
   public class EurekaconsumerApplication {
       public static void main(String[] args) {
           SpringApplication.run(EurekaconsumerApplication.class, args);
       }
       @Bean
       @LoadBalanced//开启负载均衡
       RestTemplate restTemplate() {
           return new RestTemplate()
       }
   }
   ```

3. application.properties配置

   ```properties
   #微服务名称
   spring.application.name=eureka-consumer
   #修改端口
   server.port=8003
   eureka.client.serviceUrl.defaultZone=http://localhost:8001/eureka/
   #修改缓存清理的时间间隔，以确保不会调用已出现异常的服务提供者
   eureka.client.registry-fetch-interval-seconds=30
   ```

4. 调用RestTemplate服务

   ```java
   @RestController
   @Autowired
   private RestTemplate restTemplate;
   @RequestMapping(value = "/consume/remote", method = RequestMethod.GET)
   public String service() {
       return restTemplate.getForEntity("http://EUREKACLIENT/serviceProducer", String.class).getBody();
   }
   ```

   上述代码定义了一个名为service的服务，服务地址为`"/consume/remote"` ，通过RestTemplate调用远程服务。其中，`"/EUREKA-CLIENT"`为服务提供者的实例名称，serviceProducer为服务@RequestMapping映射后的地址。

5. 调用服务

   在浏览器地址栏中输入http://localhost:8003/consume/remote访问服务。

6. 定义基于Feign接口的服务

   基于Feign接口的服务调用简单方便，上述代码在EurekaconsumerApplication中已经通过加入@EnableFeignClients开启了对Feign的支持，这里只需要应用程序定义Feign接口即可。

   定义Feign接口分为2步：首先通过@FeignClient("serviceProducerName")定义需要代理的服务实例名（”EUREKA-CLIENT“即服务提供者的实例名称），然后定义一个调用远程服务的方法。方法中@RequestMapping的value（这里为"/serviceProducer"）需要与服务提供者的地址相对应，这样，Feign才能知道代理需要具体调用服务提供者的哪个方法。

   ```java
   //@FeignClient用于通知Feign组件对该接口进行代理（不需要编写接口实现），使用者可直接通过@Autowired注入
   //@RequestMapping表示在调用该方法时需要向/serviceProducer发送GET请求
   @FeignClient("EUREKA-CLIENT")
   public interface Servers {
       @RequestMapping(value = "/serviceProducer", method = RequestMethod.GET)
       Map serviceProducer();
   }
   ```

7. 调用基于Feign接口的服务

   ```java
   @Autowired Servers server;
   @RequesMapping(value = "/consume/feign", method = RequestMethod.GET)
   public Map serviceFeign() {
       return server.serviceProducer();
   }
   ```

   上述代码通过注入Servers并调用serviceProducer()方法来实现服务的调用。在浏览器中输入http://localhost:8003/consume/feign查看服务结果。

## 2.4 Spring Cloud Consul

## 2.5 Spring Cloud Feign

​	Feign是一种声明式、模板化的HTTP Client。它的目的是使编写Java HTTP Client变得更简单。

​	Feign通过使用Jersey和CXF等工具实现一个HTTP Client，用于构建REST或SOAP的服务。Feign还支持用户基于常用的HTTP工具包（OkHTTP、HTTPComponents）实现自定义的HTTP  Client。

​	Feign基于注解的方式将HTTP请求模板化。Feign将HTTP请求参数写入Template，极大地简化了HTTP请求。尽管Feign目前只支持基于文本的HTTP请求，不适合文件的上传和下载，但它为HTTP请求提供了更多可能性，比如请求回放等功能。

### 2.5.1 Feign的应用

（1）pom.xml依赖

```xml
<dependency>
	<groupId>org.springframwork.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
    <version>1.4.6.RELESASE</version>
</dependency>
```

（2）通过@EnableFeignClients注解开启对Feign的支持

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
public class FeignClientApplication {
    public static void main(String[] args) {
        SpringApplication.run(FeignClientApplication.class, args);
    }
}
```

（3）application.properties

```properties
#Feign服务实例的名称
spring.application.name=feignclient
#Feign服务实例的端口
server.port=9004
#注册中心的地址
eureka.client.serviceUrl.defaultZone=http://localhost:9001/eureka/
eureka.client.registry-fetch-interval-seconds=30
```

（4）创建接口和定义需要远程调用的方法

```java
@FeignClient("EUREKA-CLIENT") //定义服务实例
public interface FeignClientInterface {
    //定义服务接口
    @RequestMapping(value = "/serviceProducter", method = RequestMethod.GET)
    Map serviceProducer();
}
```

（5）调用服务

```java
@RestController
public class FeignController {
    @Autowired
    FeignClientInterface feignClientInterface;
    @RequestMapping(value = "/consume/feign", method = RequestMethod.GET)
    public Map serviceFeign() {
        return feignClientInterface.serviceProducer();
    }
}
```

### 2.5.2 Feign的常用注解

## 2.6 Spring Cloud Hystrix

## 2.7 Spring Cloud Zuul

## 2.8 Spring Cloud的链路监控
