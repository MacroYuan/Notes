# RabbitMQ

https://juejin.cn/post/6958421370623492132

~~消息队列的使用，可以对多个模块之间进行解耦，并实现异步通信，降低系统整体的响应时间。~~

![RabbitMQ的生产者-消息模型-消费者.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ef31f1a9a1dd458589a7f67c85782ca2~tplv-k3u1fbpfcp-watermark.image)

### 1. 引入依赖

```xml
  <!-- RabbitMQ的起步依赖，和spring-boot整合成一个Jar包了 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
    <version>1.5.6.RELEASE</version>
</dependency>
```



### 2. 配置host、端口等信息

```properties
# RabbitMQ配置
spring.rabbitmq.virtual-host=/
# 配置host、端口号、用户名、密码
spring.rabbitmq.host=127.0.0.1
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest

# 设置交换机、路由、队列，使用directExchange消息模型
# 自定义变量，表示本地开发环境
mq.env=local

# 设置direct消息模型中队列、交换机、路由
mq.basic.info.queue.name=${mq.env}.middleware.mq.basic.info.queue.demo1
mq.basic.info.exchange.name=${mq.env}.middleware.mq.basic.info.exchange.demo1
mq.basic.info.routing.key.name=${mq.env}.middleware.mq.basic.info.routing.key.demo1
```



### 3. 自定义注入Bean组件

官方三条准则：

- 生产者的发送确认机制
- 创建队列、交换机消息时设置持久化模式
- 消费者的确认消费ACK机制

#### 3.1 生产者的发送确认机制

RabbitMQ要求生产者在发送完消息之后进行“发送确认”，当生产者确认成功时即代表消息已经完成发送了。

```java
@Bean(name = "rabbitMQTemplate")
public RabbitTemplate rabbitTemplate() {
	//生产者确认消息是否发送过去
    connectionFactory.setPublisherConfirms(true);
    
    //生产者发送消息后，返回反馈消息
    connectionFactory.setPublisherReturns(true);
    
    //构建rabbitTemplate操作模板
    RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
    rabbitTemplate.setMandatory(true);
    
    //生产者发送消息后，如果发送成功，则打印“消息发送成功”日志信息
    rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallBack() {
        public void confirm(CorrelationData correlationData, boolean ack, String cause) {
            log.info("消息发送成功：correlationData({})，ack({})，cause({})", correlationData, ack, cause);
        }
    });
    return rabbitTemplate;
}
```

#### 3.2 创建队列、交换机、消息，设置持久化模式

持久化确保RabbitMQ队列中的消息不丢失，RabbitMQ官方建议开发者在创建队列、交换机设置持久化参数为true，也就是durable参数的值为true。同时，官方强烈建议开发者在创建消息时设置消息的持久化模式为”持久化“，这样就可以保证RabbitMQ服务器崩溃并执行重启操作后，队列、交换机仍然存在，而且该消息不会丢失。

```java
// 创建队列
@Bean(name = "basicQueue")
public Queue basicQueue() {
    return new Queue(env.getProperty("mq.basic.info.queue.nqme"), true);
}

//创建交换机
@Bean
public DirectExchange basicExchange(){
    return new DirectExchange(env.getProperty("mq.basic.info.exchange.name"), true, false);
}

//创建绑定关系
@Bean
public Binding basicBinding() {
    return BindingBuilder.bind(basicQueue()).to(basicExchange()).with(env.getProperty("mq.basic.info.routing.key.name"));
}
```

在创建消息时设置消息的持久化模板为“持久化”：

```java
@Component
@Slf4j
public class BasicPublisher {
    
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    @Autowired
    Environment env;
    
    //发送字符串类型的消息
    public void sendMsg(String messageStr) {
        if (!Strings.isNullOrEmpty(messageStr)) {
            try {
                rabbitTemplate.setMessageConverter(new Jackson2JsonMessageConverter());
                rabbitTemplate.setExchange(env.getProperty("mq.basic.info.exchange.name"));
                rabbitTemplate.setRoutingKey(env.getProperty("mq.basic.info.routing.key.name"));
                
                //创建队列、交换机、消息
                //设置消息的持久化模式
                Message message = MessageBuilder.withBody(messageStr.getBytes("utf-8")).setDeliveryMode(MessageDeliveryMode.PERSISTENT).build();
                rabbitTemplate.convertAndSend(message);
                log.info("基本消息模型-生产者-发送消息：{}", messageStr, e.fillInStackTrace());
            }
        }
    }
}
```



#### 3.3 消费组的确认消费ACK机制

消费者的确认消费机制有三种：None、Auto、Manual

- None：不进行确认消息，也就是消费者不发送任任何务反馈信息给MQ服务器。
- Auto：消费者自动确认消费。消费者处理该消息后，需要发送一个自动的ack反馈信息给MQ服务端，之后该消息从MQ的队列中移除。其底层的实现逻辑是由RabbitMQ内置的相关组件实现自动发送确认反馈信息。
- Manual：人为手动确认消费机制。消费者处理该消息后，需要手动地“以代码的形式”发送一个ack反馈信息给MQ服务端。

消费者监听窗口为：

```java
@Bean(name = "singleListenerContainer")
public SimpleRabbitListenerContainerFactory listenerContainerFactory() {
    SimpleRabbitListenerContainerFactory containerFactory = new SimpleRabbitListenerContainerFactory();
    containerFactory.setConnectionFactory(connectionFactory);
    containerFactory.setMessageConverter(new Jackson2JsonMessageConverter());
    
    //设置消费者的个数
    containerFactory.setConcurrentConsumers(1);
    //设置消费者的最大值
    containerFactory.setMaxConcurrentConsumers(1);
    
    //设置消费者每次拉取的消息数量，即消费者一次拉取几条消息
    containerFactory.setPrefetchCount(1);
    
    //设置确认消息模型为自动确认消费AUTO，目的是防止消息丢失和消息被重复消费
    containerFactory.setAcknowledgeMode(AcknowledgeMode.AUTO);
    return containerFactory;
}
```

### 4. RabbitMQ发送、接收

#### 4.1 定义生产者

```java
@Component
@Slf4j
public class BasicPublisher {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Autowired
    Environment env;

    // 发送字符串类型的消息
    public void sendMsg(String messageStr) {
        if (!Strings.isNullOrEmpty(messageStr)) {
            try {
                rabbitTemplate.setMessageConverter(new Jackson2JsonMessageConverter());
                rabbitTemplate.setExchange(env.getProperty("mq.basic.info.exchange.name"));
                rabbitTemplate.setRoutingKey(env.getProperty("mq.basic.info.routing.key.name"));

                // 2创建队列、交换机、消息 设置持久化模式
                // 设置消息的持久化模式
                Message message = MessageBuilder.withBody(messageStr.getBytes("utf-8")).
                        setDeliveryMode(MessageDeliveryMode.PERSISTENT).build();
                rabbitTemplate.convertAndSend(message);
                log.info("基本消息模型-生产者-发送消息：{}", messageStr);

            } catch (UnsupportedEncodingException e) {
                log.error("基本消息模型-生产者-发送消息发生异常：{}", messageStr, e.fillInStackTrace());
            }
        }
    }

}
```

#### 4.2 创建消费者

```java
@Component
@Slf4j
public class BasicConsumer {

    /**
     * 监听并消费队列中的消息
     */
    @RabbitListener(queues = "${mq.basic.info.queue.name}", containerFactory = "singleListenerContainer")
    public void consumerMsg(@Payload byte[] msg) {
        try {
            String messageStr = new String(msg, "utf-8");
            log.info("基本消息模型-消费者-监听并消费到的消息：{}", messageStr);

        } catch (UnsupportedEncodingException e) {

            log.error("基本消息模型-消费者-发生异常：", e.fillInStackTrace());
        }
    }


}
```

#### 4.3 单元测试

```java
@Slf4j
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class RabbitMQTest {

    @Autowired
    private BasicPublisher basicPublisher;

    // 测试 基本消息模型，消息内容为 字符串
    @Test
    public void testBasicMessageModel() {
        String msgStr = "~~~这是一串字符串消息~~~~";
        basicPublisher.sendMsg(msgStr);
    }
}
```

