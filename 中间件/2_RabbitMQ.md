# RabbitMQ
RabbitMQ是一个由erlang开发的AMQP（Advanced Message Queue ）的开源实现。AMQP 的出现其实也是应了广大人民群众的需求，虽然在同步消息通讯的世界里有很多公开标准（如 COBAR的 IIOP ，或者是 SOAP 等），但是在异步消息处理中却不是这样，只有大企业有一些商业实现（如微软的 MSMQ ，IBM 的 Websphere MQ 等），因此，在 2006 年的 6 月，Cisco 、Redhat、iMatix 等联合制定了 AMQP 的公开标准。
RabbitMQ是由RabbitMQ Technologies Ltd开发并且提供商业支持的。该公司在2010年4月被SpringSource（VMWare的一个部门）收购。在2013年5月被并入Pivotal。其实VMWare，Pivotal和EMC本质上是一家的。不同的是VMWare是独立上市子公司，而Pivotal是整合了EMC的某些资源，现在并没有上市。
RabbitMQ官网[传送](https://www.rabbitmq.com/)

## Win10 安装RabbitMQ
下载erlang 语音环境包
配置环境变量
环境名称
ERLANG_HOME
指定路径
D:\Program Files\erl10.1\
path添加进去
%ERLANG_HOME%\bin
安装RabbitMQ 启动
启动成功以后访问 RabbitMQ 管理平台地址 http://127.0.0.1:15672
默认账号:guest/guest  用户可以自己创建新的账号

## RabbitMQ名词介绍
- **Virtual Hosts**:有点像MySQL的数据库的概率,每个Virtual Hosts相互隔离,exchange、queue、message不能互通
- **exchange**: 交换机用于分发消息到队列不存储消息,找不到则丢失
- **queue**:队列存放消息此处可以持久化
- **message**:消息

## RabbitMQ六种消息队列
1. **简单队列**:生产者直接投递消息到队列,消费者一个按照先后顺序消费,如果消费者集群将会轮询消费
2. **工作队列**:生产者直接投递消息到队列,消费者通过basicQos方法设置投递量.如果没返回成功则不继续投递消息,实现能者多劳的效果
3. **发布订阅模式**:生产者将消息投递到交换机,交换机根据不同的路由规则转发到不同的消息队列,不同的消费从消息队列中拿消息,交换机中不存储消息,如果交换机没有路由规则则消息丢失,一个消费者绑定一个队列
路由策略,消费者可以绑定多个队列
4. **路由模式Routing**:生产者将消息投递到交换机,交换机根据生产者设定的不同的key转发到有相同key的消费者,如果交换机没有路由key匹配消息丢失,一个消费者绑定一个队列,消费者可以绑定多个队列
   路由策略
5. **通配符模式Topics**:和路由模式类似,但是他的key是可以模糊匹配的，*匹配一个单词，#匹配0或者多个单词消费者可以绑定多个队列
6. **RPC**:暂无

## RabbitMQ交换机(四种常用+默认交换机+死信交换机)
1. **Direct exchange(直连交换机)**:根据消息携带的路由键（routing key）将消息投递给对应队列的，步骤如下：
>1. 将一个队列绑定到某个交换机上，同时赋予该绑定一个路由键（routing key）
>2. 当一个携带着路由值为R的消息被发送给直连交换机时，交换机会把它路由给绑定值同样为R的队>列。
2. **Fanout exchange(扇型交换机)**:扇型交换机（funout exchange）将消息路由给绑定到它身上的所有队列。不同于直连交换机，路由键在此类型上不启任务作用。如果N个队列绑定到某个扇型交换机上，当有消息发送给此扇型交换机时，交换机会将消息的发送给这所有的N个队列
3. **Topic exchange(主题交换机)**:主题交换机（topic exchanges）中，队列通过路由键绑定到交换机上，然后，交换机根据消息里的路由值，将消息路由给一个或多个绑定队列。
>扇型交换机和主题交换机异同： 
> - 对于扇型交换机路由键是没有意义的，只要有消息，它都发送到它绑定的所有队列上
> - 对于主题交换机，路由规则由路由键决定，只有满足路由键的规则，消息才可以路由到对应的队列上
路由策略
4. **`Headers exchange`(头交换机)**:类似主题交换机，但是头交换机使用多个消息属性来代替路由键建立路由规则。通过判断消息头的值能否与指定的绑定相匹配来确立路由规则。 
>此交换机有个重要参数：”x-match”
> - 当”x-match”为“any”时，消息头的任意一个值被匹配就可以满足条件
> - 当”x-match”设置为“all”的时候，就需要消息头的所有值都匹配成功

4. **`default exchange`(默认交换机 )**:实际上是一个由RabbitMQ预先声明好的名字为空字符串的直连交换机（direct exchange）。它有一个特殊的属性使得它对于简单应用特别有用处：那就是每个新建队列（queue）都会自动绑定到默认交换机上，绑定的路由键（routing key）名称与队列名称相同。
> 如：当你声明了一个名为”hello”的队列，RabbitMQ会自动将其绑定到默认交换机上，绑定（binding）的路由键名称也是为”hello”。因此，当携带着名为”hello”的路由键的消息被发送到默认交换机的时候，此消息会被默认交换机路由至名为”hello”的队列中。即默认交换机看起来貌似能够直接将消息投递给队列 
5. **`Dead Letter Exchange`（死信交换机）**:在默认情况，如果消息在投递到交换机时，交换机发现此消息没有匹配的队列，则这个消息将被悄悄丢弃。为了解决这个问题，RabbitMQ中有一种交换机叫死信交换机。当消费者不能处理接收到的消息时，将这个消息重新发布到另外一个队列中，等待重试或者人工干预。这个过程中的exchange和queue就是所谓的”Dead Letter Exchange 和 Queue”

### 交换机的属性
除交换机类型外，在声明交换机时还可以附带许多其他的属性，其中最重要的几个分别是：
- `Name`：交换机名称
- `Durability`：是否持久化。如果持久性，则RabbitMQ重启后，交换机还存在
- `Auto-delete`：当所有与之绑定的消息队列都完成了对此交换机的使用后，删掉它
- `Arguments`：扩展参数

## Spring整合RabbitMQ
### 引入依赖
```maven
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>3.6.5</version>
</dependency>
```
### 创建RabbitMQ连接工具类
```java
public class MQConnectionUtils {

	public static Connection newConnection() throws IOException, TimeoutException {
		// 1.定义连接工厂
		ConnectionFactory factory = new ConnectionFactory();
		// 2.设置服务器地址
		factory.setHost("127.0.0.1");
		// 3.设置协议端口号
		factory.setPort(5672);
		// 4.设置vhost
		factory.setVirtualHost("/test001_host");
		// 5.设置用户名称
		factory.setUsername("test001");
		// 6.设置用户密码
		factory.setPassword("123456");
		// 7.创建新的连接
		Connection newConnection = factory.newConnection();
		return newConnection;
	}

}
```
### 简单队列实现 
#### 生产者投递消息
```java
public class Producer {
	private static final String QUEUE_NAME = "test_queue";

	public static void main(String[] args) throws IOException, TimeoutException {
		// 1.获取连接
		Connection newConnection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = newConnection.createChannel();
		// 3.创建队列声明
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		String msg = "test_yushengjun110";
		System.out.println("生产者发送消息:" + msg);
		// 4.发送消息
		channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
		channel.close();
		newConnection.close();
	}

}
```
#### 消费者消费消息
```java
public class Customer {
	private static final String QUEUE_NAME = "test_queue";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("002");
		// 1.获取连接
		Connection newConnection = MQConnectionUtils.newConnection();
		// 2.获取通道
		Channel channel = newConnection.createChannel();
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		DefaultConsumer defaultConsumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msgString = new String(body, "UTF-8");
				System.out.println("消费者获取消息:" + msgString);
			}
		};
		// 3.监听队列 true表示自动签收
		channel.basicConsume(QUEUE_NAME, true, defaultConsumer);

	}

}
```
### 工作队列实现 
#### 生产者投递消息
```java
public class Producer {
	private static final String QUEUE_NAME = "test_queue";

	public static void main(String[] args) throws IOException, TimeoutException {
		// 1.获取连接
		Connection newConnection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = newConnection.createChannel();
		// 3.创建队列声明
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		channel.basicQos(1);// 保证一次只分发一次 限制发送给同一个消费者 不得超过一条消息
		for (int i = 1; i <= 50; i++) {
			String msg = "test_yushengjun" + i;
			System.out.println("生产者发送消息:" + msg);
			// 4.发送消息
			channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
		}
		channel.close();
		newConnection.close();
	}

}
```
#### 消费者消费消息
```java
public class Customer1 {
	private static final String QUEUE_NAME = "test_queue";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("001");
		// 1.获取连接
		Connection newConnection = MQConnectionUtils.newConnection();
		// 2.获取通道
		final Channel channel = newConnection.createChannel();
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		channel.basicQos(1);// 保证一次只分发一次 限制发送给同一个消费者 不得超过一条消息
		DefaultConsumer defaultConsumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msgString = new String(body, "UTF-8");
				System.out.println("消费者获取消息:" + msgString);
				try {
					Thread.sleep(1000);
				} catch (Exception e) {

				} finally {
					// 手动回执消息
					channel.basicAck(envelope.getDeliveryTag(), false);
				}
			}
		};
		// 3.监听队列
		channel.basicConsume(QUEUE_NAME, false, defaultConsumer);

	}

}
```

### 发布订阅模式实现 
#### 生产者投递消息
```java
public class ProducerFanout {
	private static final String EXCHANGE_NAME = "fanout_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.绑定的交换机 参数1交互机名称 参数2 exchange类型
		channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
		String msg = "fanout_exchange_msg";
		// 4.发送消息
		channel.basicPublish(EXCHANGE_NAME, "", null, msg.getBytes());
		// System.out.println("生产者发送msg：" + msg);
		// // 5.关闭通道、连接
		// channel.close();
		// connection.close();
		// 注意：如果消费没有绑定交换机和队列，则消息会丢失

	}

}
```
#### 消费者消费消息
```java
// 消费者一
public class ConsumerEmailFanout {
	private static final String QUEUE_NAME = "consumerFanout_email";
	private static final String EXCHANGE_NAME = "fanout_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("邮件消费者启动");
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.消费者关联队列
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		// 4.消费者绑定交换机 参数1 队列 参数2交换机 参数3 routingKey
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "");
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg = new String(body, "UTF-8");
				System.out.println("消费者获取生产者消息:" + msg);
			}
		};
		// 5.消费者监听队列消息
		channel.basicConsume(QUEUE_NAME, true, consumer);
	}

}

// 消费者二
public class ConsumerSMSFanout {
	private static final String QUEUE_NAME = "ConsumerFanout_sms";
	private static final String EXCHANGE_NAME = "fanout_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("短信消费者启动");
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.消费者关联队列
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		// 4.消费者绑定交换机 参数1 队列 参数2交换机 参数3 routingKey
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "");
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg = new String(body, "UTF-8");
				System.out.println("消费者获取生产者消息:" + msg);
			}
		};
		// 5.消费者监听队列消息
		channel.basicConsume(QUEUE_NAME, true, consumer);
	}

}
```
### 路由模式Routing实现 
#### 生产者投递消息
```java
public class ProducerDirect {
	private static final String EXCHANGE_NAME = "direct_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.绑定的交换机 参数1交互机名称 参数2 exchange类型
		channel.exchangeDeclare(EXCHANGE_NAME, "direct");
		String routingKey = "info";
		String msg = "direct_exchange_msg" + routingKey;
		// 4.发送消息
		channel.basicPublish(EXCHANGE_NAME, routingKey, null, msg.getBytes());
		System.out.println("生产者发送msg：" + msg);
		// // 5.关闭通道、连接
		// channel.close();
		// connection.close();
		// 注意：如果消费没有绑定交换机和队列，则消息会丢失

	}

}
```
#### 消费者消费消息
```java
// 消费者一
public class ConsumerEmailDirect {
	private static final String QUEUE_NAME = "consumer_direct_email";
	private static final String EXCHANGE_NAME = "direct_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("邮件消费者启动");
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.消费者关联队列
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		// 4.消费者绑定交换机 参数1 队列 参数2交换机 参数3 routingKey
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "error");
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "info");
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg = new String(body, "UTF-8");
				System.out.println("消费者获取生产者消息:" + msg);
			}
		};
		// 5.消费者监听队列消息
		channel.basicConsume(QUEUE_NAME, true, consumer);
	}

}

// 消费者二
public class ConsumerSMSDirect {
	private static final String QUEUE_NAME = "consumer_direct_sms";
	private static final String EXCHANGE_NAME = "direct_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("短信消费者启动");
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.消费者关联队列
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		// 4.消费者绑定交换机 参数1 队列 参数2交换机 参数3 routingKey
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "error");
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg = new String(body, "UTF-8");
				System.out.println("消费者获取生产者消息:" + msg);
			}
		};
		// 5.消费者监听队列消息
		channel.basicConsume(QUEUE_NAME, true, consumer);
	}

}
```

### 通配符模式Topics 
#### 生产者投递消息
```java
public class ProducerDirect {
	private static final String EXCHANGE_NAME = "topic_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.绑定的交换机 参数1交互机名称 参数2 exchange类型
		channel.exchangeDeclare(EXCHANGE_NAME, "topic");
		String routingKey = "log.info.error";
		String msg = "topic_exchange_msg" + routingKey;
		// 4.发送消息
		channel.basicPublish(EXCHANGE_NAME, routingKey, null, msg.getBytes());
		System.out.println("生产者发送msg：" + msg);
		// // 5.关闭通道、连接
		channel.close();
		connection.close();
		// 注意：如果消费没有绑定交换机和队列，则消息会丢失

	}

}
```
#### 消费者消费消息
```java
// 消费者一
public class ConsumerEmailDirect {
	private static final String QUEUE_NAME = "consumer_topic_email";
	private static final String EXCHANGE_NAME = "topic_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("邮件消费者启动");
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.消费者关联队列
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "log.#");
		// 4.消费者绑定交换机 参数1 队列 参数2交换机 参数3 routingKey
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg = new String(body, "UTF-8");
				System.out.println("消费者获取生产者消息:" + msg);
			}
		};
		// 5.消费者监听队列消息
		channel.basicConsume(QUEUE_NAME, true, consumer);
	}

}

// 消费者二
public class ConsumerSMSDirect {
	private static final String QUEUE_NAME = "consumer_topic_sms";
	private static final String EXCHANGE_NAME = "topic_exchange";

	public static void main(String[] args) throws IOException, TimeoutException {
		System.out.println("短信消费者启动");
		// 1.创建新的连接
		Connection connection = MQConnectionUtils.newConnection();
		// 2.创建通道
		Channel channel = connection.createChannel();
		// 3.消费者关联队列
		channel.queueDeclare(QUEUE_NAME, false, false, false, null);
		// 4.消费者绑定交换机 参数1 队列 参数2交换机 参数3 routingKey
		channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "log.*");
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg = new String(body, "UTF-8");
				System.out.println("消费者获取生产者消息:" + msg);
			}
		};
		// 5.消费者监听队列消息
		channel.basicConsume(QUEUE_NAME, true, consumer);
	}

}

```

## SpringBoot整合RabbitMQ
### 引入依赖
```maven
<!-- 添加springboot对amqp的支持 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```
### 编写YML配置
```yml
spring:
  rabbitmq:
  ####连接地址
    host: 127.0.0.1
   ####端口号   
    port: 5672
   ####账号 
    username: guest
   ####密码  
    password: guest
   ### 地址
    virtual-host: /
```
### 生产者代码 
```java
// 绑定队列交换机
@Component
public class FanoutConfig {

	// 邮件队列
	private String FANOUT_EMAIL_QUEUE = "fanout_eamil_queue";

	// 短信队列
	private String FANOUT_SMS_QUEUE = "fanout_sms_queue";
	// 短信队列
	private String EXCHANGE_NAME = "fanoutExchange";

	// 1.定义队列邮件
	@Bean
	public Queue fanOutEamilQueue() {
		return new Queue(FANOUT_EMAIL_QUEUE);
	}

	@Bean
	public Queue fanOutSmsQueue() {
		return new Queue(FANOUT_SMS_QUEUE);
	}

	// 2.定义交换机
	@Bean
	FanoutExchange fanoutExchange() {
		return new FanoutExchange(EXCHANGE_NAME);
	}

	// 3.队列与交换机绑定邮件队列
	@Bean
	Binding bindingExchangeEamil(Queue fanOutEamilQueue, FanoutExchange fanoutExchange) {
		return BindingBuilder.bind(fanOutEamilQueue).to(fanoutExchange);
	}

	// 4.队列与交换机绑定短信队列
	@Bean
	Binding bindingExchangeSms(Queue fanOutSmsQueue, FanoutExchange fanoutExchange) {
		return BindingBuilder.bind(fanOutSmsQueue).to(fanoutExchange);
	}
}

// 生产者封装
@Component
public class FanoutProducer {
	@Autowired
	private AmqpTemplate amqpTemplate;

	public void send(String queueName) {
		String msg = "my_fanout_msg:" + new Date();
		System.out.println(msg + ":" + msg);
		amqpTemplate.convertAndSend(queueName, msg);
	}
}

// 生产者使用
@RestController
public class ProducerController {
	@Autowired
	private FanoutProducer fanoutProducer;

	@RequestMapping("/sendFanout")
	public String sendFanout(String queueName) {
		fanoutProducer.send(queueName);
		return "success";
	}
}
```

### 消费者配置
```yml
spring:
  rabbitmq:
  ####连接地址
    host: 127.0.0.1
   ####端口号   
    port: 5672
   ####账号 
    username: guest
   ####密码  
    password: guest
   ### 地址
    virtual-host: /admin_host
    listener:
      simple:
        retry:
        #### 开启消费者重试
          enabled: true
         #### 最大重试次数
          max-attempts: 5
        #### 重试最大间隔时间
          max-interval: 10000
        #### 重试初始间隔时间
          initial-interval: 2000
        #### 间隔时间乘子，间隔时间*乘子=下一次的间隔时间，最大不会超过设置的最大间隔时间
          multiplier: 2 
```
### 消费者代码
```java
// @RabbitListener 监听队列名称    @RabbitHandler 监听队列有消息后的处理方法 
// 开启消费者重试机制 一定要注意配置重试次数和重试间隔  默认异常无限重试 
// 开启手动应答  @Headers Map<String, Object> headers, Channel channel
@Component
@RabbitListener(queues = "fanout_eamil_queue")
public class FanoutEamilConsumer {
	@RabbitHandler
	public void process(String msg) throws Exception {
		System.out.println("邮件消费者获取生产者消息msg:" + msg);
	}
}
```

## RabbitMQ常用介绍
### RabbitMQ应答模式
 为了确保消息不会丢失，RabbitMQ支持消息应答。消费者发送一个消息应答，告诉RabbitMQ这个消息已经接收并且处理完毕了。RabbitMQ就可以删除它了。
如果一个消费者挂掉却没有发送应答，RabbitMQ会理解为这个消息没有处理完全，然后交给另一个消费者去重新处理。这样，你就可以确认即使消费者偶尔挂掉也不会丢失任何消息了。
   没有任何消息超时限制；只有当消费者挂掉时，RabbitMQ才会重新投递。即使处理一条消息会花费很长的时间。
消息应答是默认打开的。我们通过显示的设置autoAsk=true关闭这种机制。现即自动应答开，一旦我们完成任务，消费者会自动发送应答。通知RabbitMQ消息已被处理，可以从内存删除。如果消费者因宕机或链接失败等原因没有发送ACK（不同于ActiveMQ，在RabbitMQ里，消息没有过期的概念），则RabbitMQ会将消息重新发送给其他监听在队列的下一个消费者。

### RabbitMQ公平转发(实现工作队列)
消息转发机制是平均分配，这样就会出现俩个消费者，奇数的任务很耗时，偶数的任何工作量很小，造成的原因就是近当消息到达队列进行转发消息。并不在乎有多少任务消费者并未传递一个应答给RabbitMQ。仅仅盲目转发所有的奇数给一个消费者，偶数给另一个消费者。
   为了解决这样的问题，我们可以使用basicQos方法，传递参数为prefetchCount= 1。这样告诉RabbitMQ不要在同一时间给一个消费者超过一条消息。
   换句话说，只有在消费者空闲的时候会发送下一条信息。调度分发消息的方式，也就是告诉RabbitMQ每次只给消费者处理一条消息，也就是等待消费者处理完毕并自己对刚刚处理的消息进行确认之后，才发送下一条消息，防止消费者太过于忙碌，也防止它太过去清闲。
通过 设置channel.basicQos(1);

### RabbitMQ生产者保证消息投递成功
使用AMQP的事物机制或者Confirm 模式
事务模式:
 - txSelect  将当前channel设置为transaction模式
 - txCommit  提交当前事务
 - txRollback  事务回滚
Confirm 模式
 生产者通过调用channel的confirmSelect方法将channel设置为confirm模式，(注意一点，已经在transaction事务模式的channel是不能再设置成confirm模式的，即这两种模式是不能共存的)
   1. 普通confirm模式，每发送一条消息，调用waitForConfirms()方法等待服务端confirm，这实际上是一种串行的confirm，每publish一条消息之后就等待服务端confirm，如果服务端返回false或者超时时间内未返回，客户端进行消息重传；
   2. 批量confirm模式，每发送一批消息之后，调用waitForConfirms()方法，等待服务端confirm，这种批量确认的模式极大的提高了confirm效率，但是如果一旦出现confirm返回false或者超时的情况，客户端需要将这一批次的消息全部重发，这会带来明显的重复消息，如果这种情况频繁发生的话，效率也会不升反降；


### RabbitMQ消费者防止重复消费
RabbitMQ 默认情况下消费者出现异常情况会进行重试,如果要进行重试记录可以使用retry功能,解决重复消费使用全局ID可以使用消息ID或者业务ID 进行判断防止重复消费

### RabbitMQ死信队列
进入死信队列的原因: 
1. 消息被拒绝(basic.reject / basic.nack)，并且requeue = false
> 消费者丢弃消息 
>
> channel.basicNack(message.getMessageProperties().getDeliveryTag(), false, false); 

2. 消息TTL过期
3. 超过队列达到最大长度的消息

### RabbitMQ解决事务
使用最终一致性实现,正常队列外增加逻辑验证队列用于保证最终一致性

### RabbitMQ队列设置
队列的最大消息数可以在声明队列时设置参数x-max-length的值来配置，该值必须是要给非负整数。
队列的消息最大字节数可以在声明队列时设置参数x-max-length-bytes的值来配置，该值必须是一个非负整数。
如果同时设置了这两个参数值，那么最先到达限制时就会被使用。
队列的溢出操作可以在队列声明时设置参数x-overflow的值，该值是一个字符串。它的值可以是drop-head（默认值）或者reject-publish。
x-message-ttl 参数，可以设定了该队列所有消息的存活时间，时间单位是毫秒，值必须大于等于0(也可以为单个消息设定过期时间为消息设置TTL有一个问题：RabbitMQ只对处于队头的消息判断是否过期（即不会扫描队列），所以，很可能队列中已存在死消息，但是队列并不知情。这会影响队列统计数据的正确性，妨碍队列及时释放资源。)
以下是使用java代码声明配置队列最大长度为10个消息：
```java
Map<String, Object> args = new HashMap<>();
args.put("x-max-length", 10);
channel.queueDeclare("myqueue", false, false, false, args);
```

### RabbitMQ三种服务方式
1. 单机模式
2. 普通模式(节点挂了将导致部分异常) 
3. 镜像模式(任一节点挂了不影响整体服务)