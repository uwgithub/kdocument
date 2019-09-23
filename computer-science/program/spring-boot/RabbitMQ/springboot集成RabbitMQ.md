#  springboot集成RabbitMQ                                                                   
## 目录                                                                
- [基本概念](#基本概念)                                                        
- [三种路由模式](#三种路由模式) 
- [编码参考](#编码参考) 


#基本概念
RabbitMQ是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用Erlang语言编写的，而集群和故障转移是构建在开放电信平台框架上的。所有主要的编程语言均有与代理接口通讯的客户端库。

支持多种客户端，如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。

RabbitMQ基本概念


Message
消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一系列的可选属性组成，这些属性包括routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等。

Publisher
消息的生产者，也是一个向交换器发布消息的客户端应用程序。

Exchange
交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。

Binding
绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。

Queue
消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。

Connection
网络连接，比如一个TCP连接。

Channel
信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接。

Consumer
消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。

Virtual Host
虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 / 。

Broker
表示消息队列服务器实体。

Exchange 类型
Exchange分发消息时根据类型的不同分发策略有区别，目前共四种类型：direct、fanout、topic、headers 。下面只讲前三种模式。

1.Direct模式
消息中的路由键（routing key）如果和 Binding 中的 binding key 一致， 交换器就将消息发到对应的队列中。路由键与队列名完全匹配

2.Topic模式
topic 交换器通过模式匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。它同样也会识别两个通配符：符号“#”和符号“*”。#匹配0个或多个单词，*匹配一个单词。

3.Fanout模式
每个发到 fanout 类型交换器的消息都会分到所有绑定的队列上去。fanout 交换器不处理路由键，只是简单的将队列绑定到交换器上，每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上。很像子网广播，每台子网内的主机都获得了一份复制的消息。fanout 类型转发消息是最快的。
 


#三种路由模式
###1.使用Direct路由模式
1.1.配置队列

package com.rabbitmq.config;
 
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
 
@Configuration
public class RabbitMQConfig {
    static final String QUEUE = "direct_queue";
    /**
     * Direct模式
     * @return
     */
    @Bean
    public Queue directQueue() {
        // 第一个参数是队列名字， 第二个参数是指是否持久化
        return new Queue(QUEUE, true);
    }
}
1.2创建一个实体类

package com.rabbitmq.model;
 
import lombok.Data;
import java.io.Serializable;
 
@Data
public class User implements Serializable {
 
    private static final long serialVersionUID = 1L;
 
    private String userId;
 
    private String name;
}
1.3发送者

package com.rabbitmq.config;
 
import com.rabbitmq.model.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class Sender {
 
    @Autowired
    private AmqpTemplate amqpTemplate;
 
    public void sendDirectQueue() {
        User user = new User();
        user.setUserId("123456");
        user.setName("张三");
        log.info("【sendDirectQueue已发送消息】");
         // 第一个参数是指要发送到哪个队列里面， 第二个参数是指要发送的内容
        this.amqpTemplate.convertAndSend(RabbitMQConfig.QUEUE, user);
    }
 
}
1.4接收者

package com.rabbitmq.config;
 
import com.rabbitmq.model.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class Receiver {
 
    // queues是指要监听的队列的名字
    @RabbitListener(queues = RabbitMQConfig.QUEUE)
    public void receiverDirectQueue(User user) {
        log.info("【receiverDirectQueue监听到消息】" + user.toString());
    }
 
}
1.5controller

package com.rabbitmq.controller;
 
import com.rabbitmq.config.Sender;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
 
@RestController
public class TestController {
 
    @Autowired
    private Sender sender;
 
    @GetMapping("/sendDirectQueue")
    public Object sendDirectQueue() {
        sender.sendDirectQueue();
        return "ok";
    }
}
1.6测试，访问http://localhost:8080/sendDirectQueue，查看日志输出

2019-03-06 10:18:55.901  INFO 8772 --- [io-8080-exec-10] com.rabbitmq.config.Sender           : 【sendDirectQueue已发送消息】
2019-03-06 10:18:55.997  INFO 8772 --- [cTaskExecutor-1] com.rabbitmq.config.Receiver         : 【receiverDirectQueue监听到消息】User(userId=123456, name=张三)
注意：发送者与接收者的Queue名字一定要相同，否则接收收不到消息。

###2.使用Topic通配符模式
2.1配置队列

package com.rabbitmq.config;
 
import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.core.TopicExchange;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
 
@Configuration
public class RabbitMQConfig {
    
    public static final String TOPIC_QUEUE1 = "topic.queue1";
    public static final String TOPIC_QUEUE2 = "topic.queue2";
    public static final String TOPIC_EXCHANGE = "topic.exchange";
 
    /**
     * Topic模式
     * @return
     */
    @Bean
    public Queue topicQueue1() {
        return new Queue(TOPIC_QUEUE1);
    }
    @Bean
    public Queue topicQueue2() {
        return new Queue(TOPIC_QUEUE2);
    }
    @Bean
    public TopicExchange topicExchange() {
        return new TopicExchange(TOPIC_EXCHANGE);
    }
    @Bean
    public Binding topicBinding1() {
        return BindingBuilder.bind(topicQueue1()).to(topicExchange()).with("topic.message");
    }
    @Bean
    public Binding topicBinding2() {
        return BindingBuilder.bind(topicQueue2()).to(topicExchange()).with("topic.#");
    }
 
}
2.2创建一个实体类（和上面一样）

2.3发送者

package com.rabbitmq.config;
 
import com.rabbitmq.model.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class Sender {
 
    @Autowired
    private AmqpTemplate amqpTemplate;
 
    public void sendTopic() {
        User user1 = new User();
        user1.setUserId("123456");
        user1.setName("张三");
 
        User user2 = new User();
        user2.setUserId("456789");
        user2.setName("李四");
 
        log.info("【sendTopic已发送消息】");
        // 第一个参数：TopicExchange名字
        // 第二个参数：Route-Key
        // 第三个参数：要发送的内容
        this.amqpTemplate.convertAndSend(RabbitMQConfig.TOPIC_EXCHANGE, "topic.message", user1 );
        this.amqpTemplate.convertAndSend(RabbitMQConfig.TOPIC_EXCHANGE, "topic.topic", user2);
    }
 
}
2.4接收者

package com.rabbitmq.config;
 
import com.rabbitmq.model.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class Receiver {
    // queues是指要监听的队列的名字
    @RabbitListener(queues = RabbitMQConfig.TOPIC_QUEUE1)
    public void receiveTopic1(User user) {
        log.info("【receiveTopic1监听到消息】" + user.toString());
    }
    @RabbitListener(queues = RabbitMQConfig.TOPIC_QUEUE2)
    public void receiveTopic2(User user) {
        log.info("【receiveTopic2监听到消息】" + user.toString());
    }
 
}
2.5controller

package com.rabbitmq.controller;
 
import com.rabbitmq.config.Sender;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
 
@RestController
public class TestController {
 
    @Autowired
    private Sender sender;
 
    @GetMapping("/sendTopic")
    public Object sendTopic() {
        sender.sendTopic();
        return "ok";
    }
}
2.6测试，访问http://localhost:8080/sendTopic，查看日志输出

2019-03-06 10:30:24.983  INFO 6504 --- [nio-8080-exec-1] com.rabbitmq.config.Sender           : 【sendTopic已发送消息】
2019-03-06 10:30:25.048  INFO 6504 --- [cTaskExecutor-1] com.rabbitmq.config.Receiver         : 【receiveTopic2监听到消息】User(userId=123456, name=张三)
2019-03-06 10:30:25.048  INFO 6504 --- [cTaskExecutor-1] com.rabbitmq.config.Receiver         : 【receiveTopic1监听到消息】User(userId=123456, name=张三)
2019-03-06 10:30:25.049  INFO 6504 --- [cTaskExecutor-1] com.rabbitmq.config.Receiver         : 【receiveTopic2监听到消息】User(userId=456789, name=李四)
由日志记录可以看出topic.message可以被receiveTopic1和receiveTopic2所接收，而topic.topic只能被receiveTopic2接收。

是因为topic模式的路由键支持模糊匹配。符号“#”匹配一个或多个词，符号“*”匹配不多不少一个词。

###3.使用Fanout订阅模式
3.1配置队列

package com.rabbitmq.config;
 
import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
 
@Configuration
public class RabbitMQConfig {
    
    public static final String TOPIC_QUEUE1 = "topic.queue1";
    public static final String TOPIC_QUEUE2 = "topic.queue2";
 
    public static final String FANOUT_EXCHANGE = "fanout.exchange";
 
 
    /**
     * 直接使用topic时所创建的队列
     * @return
     */
    @Bean
    public Queue topicQueue1() {
        return new Queue(TOPIC_QUEUE1);
    }
    @Bean
    public Queue topicQueue2() {
        return new Queue(TOPIC_QUEUE2);
    }
    
 
    /**
     * Fanout模式
     * Fanout 就是我们熟悉的广播模式或者订阅模式，给Fanout交换机发送消息，绑定了这个交换机的所有队列都收到这个消息。
     * @return
     */
    @Bean
    public FanoutExchange fanoutExchange() {
        return new FanoutExchange(FANOUT_EXCHANGE);
    }
    @Bean
    public Binding fanoutBinding1() {
        return BindingBuilder.bind(topicQueue1()).to(fanoutExchange());
    }
    @Bean
    public Binding fanoutBinding2() {
        return BindingBuilder.bind(topicQueue2()).to(fanoutExchange());
    }
 
}
3.2创建一个实体类（和上面一样）

3.3发送者

package com.rabbitmq.config;
 
import com.rabbitmq.model.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class Sender {
 
    @Autowired
    private AmqpTemplate amqpTemplate;
 
    public void sendFanout() {
        User user = new User();
        user.setUserId("123456");
        user.setName("张三");
        log.info("【sendFanout已发送消息】");
        // 注意， 这里的第2个参数为空。
        // 因为fanout 交换器不处理路由键，只是简单的将队列绑定到交换器上，
        // 每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上
        this.amqpTemplate.convertAndSend(RabbitMQConfig.FANOUT_EXCHANGE, "", user);
    }
 
}
3.4接收者

package com.rabbitmq.config;
 
import com.rabbitmq.model.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class Receiver {
 
    // queues是指要监听的队列的名字
    @RabbitListener(queues = RabbitMQConfig.TOPIC_QUEUE1)
    public void receiveTopic1(User user) {
        log.info("【receiveTopic1监听到消息】" + user.toString());
    }
    @RabbitListener(queues = RabbitMQConfig.TOPIC_QUEUE2)
    public void receiveTopic2(User user) {
        log.info("【receiveTopic2监听到消息】" + user.toString());
    }
 
}
3.5controller

package com.rabbitmq.controller;
 
import com.rabbitmq.config.Sender;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
 
@RestController
public class TestController {
 
    @Autowired
    private Sender sender;
 
    @GetMapping("/sendFanout")
    public Object sendFanout() {
        sender.sendFanout();
        return "ok";
    }
}
3.6测试，访问http://localhost:8080/sendFanout，查看日志输出

2019-03-06 11:00:47.817  INFO 5848 --- [nio-8080-exec-1] com.rabbitmq.config.Sender           : 【sendFanout已发送消息】
2019-03-06 11:00:47.845  INFO 5848 --- [cTaskExecutor-1] com.lzc.rabbitmq.config.Receiver         : 【receiveTopic1监听到消息】User(userId=123456, name=张三)
2019-03-06 11:00:47.845  INFO 5848 --- [cTaskExecutor-1] com.lzc.rabbitmq.config.Receiver         : 【receiveTopic2监听到消息】User(userId=123456, name=张三)
由日志输出可以看出，两个接收者都接收到了消息，因为交换机FANOUT_EXCHANGE绑定了两个队列。


#编码参考
https://www.jianshu.com/p/4de4580c8f87
https://blog.csdn.net/qq_37552993/article/details/88218804
 