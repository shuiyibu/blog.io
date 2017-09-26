 

# 深入浅出JMS

# 1 JMS基本概念

> 摘要：The Java Message Service (JMS) API is a messaging standard that allows application components based on the Java Platform Enterprise Edition (Java EE) to create, send, receive, and read messages. It enables distributed communication that is loosely coupled, reliable, and asynchronous.
>
> JMS（JAVA Message Service,java消息服务）API是一个消息服务的标准或者说是规范，允许应用程序组件基于JavaEE平台创建、发送、接收和读取消息。它使分布式通信耦合度更低，消息服务更加可靠以及异步性。

这篇博文我们主要介绍J2EE中的一个重要规范JMS，因为这个规范在企业中的应用十分的广泛，也比较重要，我们主要介绍JMS的基本概念和它的模式，消息的消费以及JMS编程步骤。

1. 基本概念

   JMS是java的消息服务，JMS的客户端之间可以通过JMS服务进行==异步==的消息传输。

2. 消息模型

   ```
   ○ Point-to-Point(P2P)
   ○ Publish/Subscribe(Pub/Sub)

   ```

   即点对点和发布订阅模型

3. P2P

   1. P2P模式图 
      ![这里写图片描述](http://img.blog.csdn.net/20150630220509535)

   2. 涉及到的概念

      1. 消息队列（Queue）
      2. 发送者(Sender)
      3. 接收者(Receiver)
      4. 每个消息都被发送到一个特定的队列，接收者从队列中获取消息。队列保留着消息，直到他们被消费或超时。

   3. P2P的特点

      1. 每个消息**==只有==**一个消费者（Consumer）(即一旦被消费，消息就不再在消息队列中)
      2. 发送者和接收者之间在时间上没有依赖性，也就是说当发送者发送了消息之后，不管接收者有没有正在运行，它不会影响到消息被发送到队列
      3. 接收者在成功接收消息之后需向队列应答成功

      如果你希望发送的每个消息都应该被成功处理的话，那么你需要P2P模式。

4. Pub/Sub

   1. Pub/Sub模式图 
      ![这里写图片描述](http://img.blog.csdn.net/20150630221227522)

   2. 涉及到的概念

      1. 主题（Topic）
      2. 发布者（Publisher）
      3. 订阅者（Subscriber） 
         客户端将消息发送到主题。多个发布者将消息发送到Topic,系统将这些消息传递给多个订阅者。

   3. Pub/Sub的特点

      1. 每个消息可以有多个消费者
      2. 发布者和订阅者之间有时间上的依赖性。针对某个主题（Topic）的订阅者，它必须创建一个订阅者之后，才能消费发布者的消息，而且为了消费消息，订阅者必须保持运行的状态。
      3. 为了缓和这样严格的时间相关性，JMS允许订阅者创建一个可持久化的订阅。这样，即使订阅者没有被激活（运行），它也能接收到发布者的消息。

      如果你希望发送的消息可以不被做任何处理、或者被一个消息者处理、或者可以被多个消费者处理的话，那么可以采用Pub/Sub模型

5. 消息的消费 
   在JMS中，消息的产生和消费是异步的。对于消费来说，JMS的消费者可以通过两种方式来消费消息。 
   ○ 同步 
   订阅者或接收者调用receive方法来接收消息，receive方法在能够接收到消息之前（或超时之前）将一直阻塞 
   ○ 异步 
   订阅者或接收者可以注册为一个消息监听器。当消息到达之后，系统自动调用监听器的onMessage方法。

6. JMS编程模型

   (1) ConnectionFactory

   创建Connection对象的工厂，针对两种不同的jms消息模型，分别有QueueConnectionFactory和TopicConnectionFactory两种。可以通过JNDI来查找ConnectionFactory对象。

   (2) Destination

   Destination的意思是消息生产者的消息发送目标或者说消息消费者的消息来源。对于消息生产者来说，它的Destination是某个队列（Queue）或某个主题（Topic）;对于消息消费者来说，它的Destination也是某个队列或主题（即消息来源）。

   所以，Destination实际上就是两种类型的对象：Queue、Topic可以通过JNDI来查找Destination。

   (3) Connection

   Connection表示在客户端和JMS系统之间建立的链接（对TCP/IP socket的包装）。Connection可以产生一个或多个Session。跟ConnectionFactory一样，Connection也有两种类型：QueueConnection和TopicConnection。

   (4) Session

   Session是我们操作消息的接口。可以通过session创建生产者、消费者、消息等。Session提供了事务的功能。当我们需要使用session发送/接收多个消息时，可以将这些发送/接收动作放到一个事务中。同样，也分QueueSession和TopicSession。

   (5) 消息的生产者

   消息生产者由Session创建，并用于将消息发送到Destination。同样，消息生产者分两种类型：QueueSender和TopicPublisher。可以调用消息生产者的方法（send或publish方法）发送消息。

   (6) 消息消费者

   消息消费者由Session创建，用于接收被发送到Destination的消息。两种类型：QueueReceiver和TopicSubscriber。可分别通过session的createReceiver(Queue)或createSubscriber(Topic)来创建。当然，也可以session的creatDurableSubscriber方法来创建持久化的订阅者。

   (7) MessageListener

   消息监听器。如果注册了消息监听器，一旦消息到达，将自动调用监听器的onMessage方法。EJB中的MDB（Message-Driven Bean）就是一种MessageListener。

7. 企业消息系统的好处

我们先来看看下图，应用程序A将Message发送到服务器上，然后应用程序B从服务器中接收A发来的消息，通过这个图我们一起来分析一下JMS的好处： 
![这里写图片描述](http://img.blog.csdn.net/20150630221818616)

1. 提供消息灵活性
2. 松散耦合
3. 异步性

对于JMS的基本概念我们就介绍这么多，下篇博文介绍一种JMS的实现。

 

# 2 ActiveMQ简单介绍以及安装

现实的企业中，对于消息通信的应用一直都非常的火热，而且在J2EE的企业应用中扮演着特殊的角色，所以对于它研究是非常有必要的。

上篇博文[深入浅出JMS(一)–JMS基本概念](http://blog.csdn.net/jiuqiyuliang/article/details/46701559)，我们介绍了消息通信的规范JMS，我们这篇博文介绍一款开源的JMS具体实现——ActiveMQ。ActiveMQ是一个易于使用的消息中间件。

### 消息中间件

我们简单的介绍一下消息中间件，对它有一个基本认识就好，消息中间件（MOM：Message Orient middleware）。

消息中间件有很多的用途和优点： 

- 将数据从一个应用程序传送到另一个应用程序，或者从软件的一个模块传送到另外一个模块；
- 负责建立网络通信的通道，进行数据的可靠传送。 
- **保证数据不重发，不丢失** 
- 能够实现跨平台操作，能够为不同操作系统上的软件集成技工数据传送服务

### MQ

首先简单的介绍一下MQ，MQ英文名MessageQueue，中文名也就是大家用的消息队列，干嘛用的呢，说白了就是一个消息的接受和转发的容器，可用于消息推送。

下面进入我们今天的主题，为大家介绍ActiveMQ：

### ActiveMQ

#### 简要概述ActiveMQ

```
Apache ActiveMQ ™ is the most popular and powerful open source messaging and Integration Patterns server.
Apache ActiveMQ is fast, supports many Cross Language Clients and Protocols, comes with easy to use Enterprise Integration Patterns and many advanced features while fully supporting JMS 1.1 and J2EE 1.4. 

```

ActiveMQ是由Apache出品的，一款最流行的，能力强劲的开源消息总线。ActiveMQ是一个完全支持JMS1.1和J2EE 1.4规范的 JMS Provider实现，它非常快速，支持多种语言的客户端和协议，而且可以非常容易的嵌入到企业的应用环境中，并有许多高级功能。

#### 下载ActiveMQ

官方网站：<http://activemq.apache.org/> 
现在ActiveMQ最新的版本是5.11.1，下载挺简单的，就不再截图了。

#### 运行ActiveMQ服务

1. 下载，解压缩

    

   ​

   大家现在好之后，将apache-activemq-5.11.1-bin.zip解压缩，我们可以看到它的整体目录结构：

    

   ​

   ​

    

   ​

   从它的目录来说，还是很简单的：

    

   ​

   - bin存放的是脚本文件
   - conf存放的是基本配置文件
   - data存放的是日志文件
   - docs存放的是说明文档
   - examples存放的是简单的实例
   - lib存放的是activemq所需jar包
   - webapps用于存放项目的目录

2. 启动ActiveMQ 
   我们了解activemq的基本目录，下面我们运行一下activemq服务，双击bin目录下的activemq.bat脚本文件或运行自己电脑版本下的activemq.bat，就可以看下图的效果。 
   ![这里写图片描述](http://img.blog.csdn.net/20150730230036054)

从上图我们可以看到activemq的存放地址，以及浏览器要访问的地址. 
\3. 测试

ActiveMQ默认使用的TCP连接端口是61616, 通过查看该端口的信息可以测试ActiveMQ是否成功启动 netstat -an|find “61616”

```
C:\Documents and Settings\Administrator>netstat -an|find "61616" 
TCP     0.0.0.0:61616     0.0.0.0:0       LISTENING

```

\4. 监控 
ActiveMQ默认启动时，启动了内置的jetty服务器，提供一个用于监控ActiveMQ的admin应用。 
admin：<http://127.0.0.1:8161/admin/>

用户名和密码都是admin

![这里写图片描述](http://img.blog.csdn.net/20150730230850497)
\5. 至此，服务端启动完毕

停止服务器，只需要按着Ctrl+Shift+C，之后输入y即可。

我们简单说说ActiveMQ特性，网上很多，只是为了保证博文的完整。

#### ActiveMQ特性列表

1. 多种语言和协议编写客户端。语言: Java, C, C++, C#, Ruby, Perl, Python, PHP。应用协议: OpenWire,Stomp REST,WS Notification,XMPP,AMQP
2. 完全支持JMS1.1和J2EE 1.4规范 (持久化,XA消息,事务)
3. 对Spring的支持,ActiveMQ可以很容易内嵌到使用Spring的系统里面去,而且也支持Spring2.0的特性
4. 通过了常见J2EE服务器(如 Geronimo,JBoss 4, GlassFish,WebLogic)的测试,其中通过JCA 1.5 resource adaptors的配置,可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上
5. 支持多种传送协议:in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA
6. 支持通过JDBC和journal提供高速的消息持久化
7. 从设计上保证了高性能的集群,客户端-服务器,点对点
8. 支持Ajax
9. 支持与Axis的整合
10. 可以很容易得调用内嵌JMS provider,进行测试

#### 什么情况下使用ActiveMQ?

1. 多个项目之间集成 
   (1) 跨平台 
   (2) 多语言 
   (3) 多项目
2. 降低系统间模块的耦合度，解耦 
   (1) 软件扩展性
3. 系统前后端隔离 
   (1) 前后端隔离，屏蔽高安全区

其实ActiveMQ的应用还有很多，大家可以上网查查，不再一一举例。

### 总结

ActiveMQ并不难，具有很多的优势。

下篇博文，我们做一个简单实例，真正的体会一把ActiveMQ的魅力。

 

# 3 ActiveMQ简单的HelloWorld实例

这篇博文，我们使用ActiveMQ为大家实现一种点对点的消息模型。如果你对点对点模型的认识较浅，可以看一下第一篇博文的介绍。

JMS其实并没有想象的那么高大上，看完这篇博文之后，你就知道什么叫简单，下面直接进入主题。

### 开发环境

我们使用的是ActiveMQ 5.11.1 Release的Windows版，官网最新版是ActiveMQ 5.12.0 Release，大家可以自行下载，[下载地址](http://activemq.apache.org/download-archives.html)。

需要注意的是，开发时候，要将apache-activemq-5.11.1-bin.zip解压缩后里面的activemq-all-5.11.1.jar包加入到classpath下面，这个包包含了所有jms接口api的实现。

### 搭建开发环境

- 建立项目 
  我们只需要建立一个java项目就可以了，导入jar包，项目截图： 
  ![这里写图片描述](http://img.blog.csdn.net/20150920171843767)

点对点的消息模型，只需要一个消息生成者和消息消费者，下面我们编写代码。

- 编写生产者

```java
package com.tgb.activemq;

import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.MessageProducer;
import javax.jms.Session;
import javax.jms.TextMessage;

import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;
/**
 * 消息的生产者（发送者） 
 * @author liang
 *
 */
public class JMSProducer {

    //默认连接用户名
    private static final String USERNAME = ActiveMQConnection.DEFAULT_USER;
    //默认连接密码
    private static final String PASSWORD = ActiveMQConnection.DEFAULT_PASSWORD;
    //默认连接地址
    private static final String BROKEURL = ActiveMQConnection.DEFAULT_BROKER_URL;
    //发送的消息数量
    private static final int SENDNUM = 10;

    public static void main(String[] args) {
        //连接工厂
        ConnectionFactory connectionFactory;
        //连接
        Connection connection = null;
        //会话 接受或者发送消息的线程
        Session session;
        //消息的目的地
        Destination destination;
        //消息生产者
        MessageProducer messageProducer;
        //实例化连接工厂
        connectionFactory = new ActiveMQConnectionFactory(JMSProducer.USERNAME, JMSProducer.PASSWORD, JMSProducer.BROKEURL);

        try {
            //通过连接工厂获取连接
            connection = connectionFactory.createConnection();
            //启动连接
            connection.start();
            //创建session
            session = connection.createSession(true, Session.AUTO_ACKNOWLEDGE);
            //创建一个名称为HelloWorld的消息队列
            destination = session.createQueue("HelloWorld");
            //创建消息生产者
            messageProducer = session.createProducer(destination);
            //发送消息
            sendMessage(session, messageProducer);

            session.commit();

        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            if(connection != null){
                try {
                    connection.close();
                } catch (JMSException e) {
                    e.printStackTrace();
                }
            }
        }

    }
    /**
     * 发送消息
     * @param session
     * @param messageProducer  消息生产者
     * @throws Exception
     */
    public static void sendMessage(Session session,MessageProducer messageProducer) throws Exception{
        for (int i = 0; i < JMSProducer.SENDNUM; i++) {
            //创建一条文本消息 
            TextMessage message = session.createTextMessage("ActiveMQ 发送消息" +i);
            System.out.println("发送消息：Activemq 发送消息" + i);
            //通过消息生产者发出消息 
            messageProducer.send(message);
        }

    }
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990
```

- 编写消费者

```java
package com.tgb.activemq;

import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.MessageConsumer;
import javax.jms.Session;
import javax.jms.TextMessage;

import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;
/**
 * 消息的消费者（接受者）
 * @author liang
 *
 */
public class JMSConsumer {

    private static final String USERNAME = ActiveMQConnection.DEFAULT_USER;//默认连接用户名
    private static final String PASSWORD = ActiveMQConnection.DEFAULT_PASSWORD;//默认连接密码
    private static final String BROKEURL = ActiveMQConnection.DEFAULT_BROKER_URL;//默认连接地址

    public static void main(String[] args) {
        ConnectionFactory connectionFactory;//连接工厂
        Connection connection = null;//连接

        Session session;//会话 接受或者发送消息的线程
        Destination destination;//消息的目的地

        MessageConsumer messageConsumer;//消息的消费者

        //实例化连接工厂
        connectionFactory = new ActiveMQConnectionFactory(JMSConsumer.USERNAME, JMSConsumer.PASSWORD, JMSConsumer.BROKEURL);

        try {
            //通过连接工厂获取连接
            connection = connectionFactory.createConnection();
            //启动连接
            connection.start();
            //创建session
            session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
            //创建一个连接HelloWorld的消息队列
            destination = session.createQueue("HelloWorld");
            //创建消息消费者
            messageConsumer = session.createConsumer(destination);

            while (true) {
                TextMessage textMessage = (TextMessage) messageConsumer.receive(100000);
                if(textMessage != null){
                    System.out.println("收到的消息:" + textMessage.getText());
                }else {
                    break;
                }
            }

        } catch (JMSException e) {
            e.printStackTrace();
        }

    }
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364
```

### 运行

1. 首先，启动ActiveMQ，如何启动ActiveMQ如何启动，请看第二篇博文。在浏览器中输入：<http://localhost:8161/admin/>，然后开始执行：
2. 运行发送者，eclipse控制台输出，如下图： 
   ![这里写图片描述](http://img.blog.csdn.net/20150920173405956) 
   此时，我们先看一下ActiveMQ服务器，Queues内容如下： 
   ![这里写图片描述](http://img.blog.csdn.net/20150920173620033)
   我们可以看到创建了一个名称为HelloWorld的消息队列，队列中有10条消息未被消费，我们也可以通过Browse查看是哪些消息，如下图： 
   ![这里写图片描述](http://img.blog.csdn.net/20150920174135335)
   如果这些队列中的消息，被删除，消费者则无法消费。
3. 我们继续运行一下消费者，eclipse控制台打印消息，如下： 
   ![这里写图片描述](http://img.blog.csdn.net/20150920174317150) 
   此时，我们先看一下ActiveMQ服务器，Queues内容如下： 
   ![这里写图片描述](http://img.blog.csdn.net/20150920174417511)
   我们可以看到HelloWorld的消息队列发生变化，多一个消息者，队列中的10条消息被消费了，点击Browse查看，已经为空了。 
   点击Active Consumers，我们可以看到这个消费者的详细信息： 
   ![这里写图片描述](http://img.blog.csdn.net/20150920175100179)

我们的实例到此就结束了，大家可以自己多点ActiveMQ服务器的内容，进一步熟悉ActiveMQ。

### 总结

这篇博文我们实现了点对点的消息模型以及发送的一个同步消息，是不是非常的简单？

下面博文，我们将实现一个ActiveMQ和Spring整合的实例。

[源码下载](http://download.csdn.net/detail/jiuqiyuliang/9122251)



 

# 4 Spring和ActiveMQ整合的完整实例

> 这篇博文,我们基于Spring+JMS+ActiveMQ+Tomcat，做一个Spring4.1.0和ActiveMQ5.11.1整合实例，实现了Point-To-Point的异步队列消息和PUB/SUB（发布/订阅）模型，简单实例，不包含任何业务。

### 环境准备

#### 工具

1. JDK1.6或1.7
2. Spring4.1.0
3. ActiveMQ5.11.1
4. Tomcat7.x

#### 目录结构

![这里写图片描述](http://img.blog.csdn.net/20150926133934271)

#### 所需jar包

![这里写图片描述](http://img.blog.csdn.net/20150926133955808)

### 项目的配置

#### 配置ConnectionFactory

connectionFactory是Spring用于创建到JMS服务器链接的，Spring提供了多种connectionFactory，我们介绍两个SingleConnectionFactory和CachingConnectionFactory。

SingleConnectionFactory：对于建立JMS服务器链接的请求会一直返回同一个链接，并且会忽略Connection的close方法调用。

CachingConnectionFactory：继承了SingleConnectionFactory，所以它拥有SingleConnectionFactory的所有功能，同时它还新增了缓存功能，它可以缓存Session、MessageProducer和MessageConsumer。我们使用CachingConnectionFactory来作为示例。

```xml
<bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
    </bean>12
```

Spring提供的ConnectionFactory只是Spring用于管理ConnectionFactory的，真正产生到JMS服务器链接的ConnectionFactory还得是由JMS服务厂商提供，并且需要把它注入到Spring提供的ConnectionFactory中。我们这里使用的是ActiveMQ实现的JMS，所以在我们这里真正的可以产生Connection的就应该是由ActiveMQ提供的ConnectionFactory。所以定义一个ConnectionFactory的完整代码应该如下所示：

```xml
    <!-- ActiveMQ 连接工厂 -->
    <!-- 真正可以产生Connection的ConnectionFactory，由对应的JMS服务厂商提供-->
    <!-- 如果连接网络：tcp://ip:61616；未连接网络：tcp://localhost:61616 以及用户名，密码-->
    <amq:connectionFactory id="amqConnectionFactory"
        brokerURL="tcp://192.168.3.3:61616" userName="admin" password="admin"  />

    <!-- Spring Caching连接工厂 -->
    <!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->  
    <bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
        <!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->  
        <property name="targetConnectionFactory" ref="amqConnectionFactory"></property>
        <!-- 同上，同理 -->
        <!-- <constructor-arg ref="amqConnectionFactory" /> -->
        <!-- Session缓存数量 -->
        <property name="sessionCacheSize" value="100" />
    </bean>12345678910111213141516
```

#### 配置生产者

配置好ConnectionFactory之后我们就需要配置生产者。生产者负责产生消息并发送到JMS服务器。但是我们要怎么进行消息发送呢？通常是利用Spring为我们提供的JmsTemplate类来实现的，所以**配置生产者其实最核心的就是配置消息发送的JmsTemplate**。对于消息发送者而言，它在发送消息的时候要知道自己该往哪里发，为此，我们在定义JmsTemplate的时候需要注入一个Spring提供的ConnectionFactory对象。

在利用JmsTemplate进行消息发送的时候，我们需要知道发送哪种消息类型：一个是点对点的ActiveMQQueue，另一个就是支持订阅/发布模式的ActiveMQTopic。如下所示：

```xml
    <!-- Spring JmsTemplate 的消息生产者 start-->

    <!-- 定义JmsTemplate的Queue类型 -->
    <bean id="jmsQueueTemplate" class="org.springframework.jms.core.JmsTemplate">
        <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
        <constructor-arg ref="connectionFactory" />
        <!-- 非pub/sub模型（发布/订阅），即队列模式 -->
        <property name="pubSubDomain" value="false" />
    </bean>

    <!-- 定义JmsTemplate的Topic类型 -->
    <bean id="jmsTopicTemplate" class="org.springframework.jms.core.JmsTemplate">
         <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
        <constructor-arg ref="connectionFactory" />
        <!-- pub/sub模型（发布/订阅） -->
        <property name="pubSubDomain" value="true" />
    </bean>

    <!--Spring JmsTemplate 的消息生产者 end-->12345678910111213141516171819
```

生产者如何指定目的地和发送消息？大家看源码即可，就不再这提供了。

#### 配置消费者

生产者往指定目的地Destination发送消息后，接下来就是消费者对指定目的地的消息进行消费了。那么消费者是如何知道有生产者发送消息到指定目的地Destination了呢？每个消费者对应每个目的地都需要有对应的MessageListenerContainer。对于消息监听容器而言，除了要知道监听哪个目的地之外，还需要知道到哪里去监听，也就是说它还需要知道去监听哪个JMS服务器，通过配置MessageListenerContainer的时候往里面注入一个ConnectionFactory来实现的。所以我们在配置一个MessageListenerContainer的时候有三个属性必须指定：

- 一个是表示从哪里监听的ConnectionFactory；
- 一个是表示监听什么的Destination；
- 一个是接收到消息以后进行消息处理的MessageListener。

```xml
<!-- 消息消费者 start-->

    <!-- 定义Queue监听器 -->
    <jms:listener-container destination-type="queue" container-type="default" connection-factory="connectionFactory" acknowledge="auto">
        <jms:listener destination="test.queue" ref="queueReceiver1"/>
        <jms:listener destination="test.queue" ref="queueReceiver2"/>
    </jms:listener-container>

    <!-- 定义Topic监听器 -->
    <jms:listener-container destination-type="topic" container-type="default" connection-factory="connectionFactory" acknowledge="auto">
        <jms:listener destination="test.topic" ref="topicReceiver1"/>
        <jms:listener destination="test.topic" ref="topicReceiver2"/>
    </jms:listener-container>

    <!-- 消息消费者 end -->123456789101112131415
```

#### ActiveMQ.xml

此时，Spring和JMS，ActiveMQ整合的ActiveMQ.xml已经完成，下面展示所有的xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:amq="http://activemq.apache.org/schema/core"
    xmlns:jms="http://www.springframework.org/schema/jms"
    xsi:schemaLocation="http://www.springframework.org/schema/beans   
        http://www.springframework.org/schema/beans/spring-beans-4.0.xsd   
        http://www.springframework.org/schema/context   
        http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/jms
        http://www.springframework.org/schema/jms/spring-jms-4.0.xsd
        http://activemq.apache.org/schema/core
        http://activemq.apache.org/schema/core/activemq-core-5.8.0.xsd">

    <!-- ActiveMQ 连接工厂 -->
    <!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供-->
    <!-- 如果连接网络：tcp://ip:61616；未连接网络：tcp://localhost:61616 以及用户名，密码-->
    <amq:connectionFactory id="amqConnectionFactory"
        brokerURL="tcp://192.168.3.3:61616" userName="admin" password="admin"  />

    <!-- Spring Caching连接工厂 -->
    <!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->  
    <bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
        <!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->  
        <property name="targetConnectionFactory" ref="amqConnectionFactory"></property>
        <!-- 同上，同理 -->
        <!-- <constructor-arg ref="amqConnectionFactory" /> -->
        <!-- Session缓存数量 -->
        <property name="sessionCacheSize" value="100" />
    </bean>

    <!-- Spring JmsTemplate 的消息生产者 start-->

    <!-- 定义JmsTemplate的Queue类型 -->
    <bean id="jmsQueueTemplate" class="org.springframework.jms.core.JmsTemplate">
        <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
        <constructor-arg ref="connectionFactory" />
        <!-- 非pub/sub模型（发布/订阅），即队列模式 -->
        <property name="pubSubDomain" value="false" />
    </bean>

    <!-- 定义JmsTemplate的Topic类型 -->
    <bean id="jmsTopicTemplate" class="org.springframework.jms.core.JmsTemplate">
         <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
        <constructor-arg ref="connectionFactory" />
        <!-- pub/sub模型（发布/订阅） -->
        <property name="pubSubDomain" value="true" />
    </bean>

    <!--Spring JmsTemplate 的消息生产者 end-->


    <!-- 消息消费者 start-->

    <!-- 定义Queue监听器 -->
    <jms:listener-container destination-type="queue" container-type="default" connection-factory="connectionFactory" acknowledge="auto">
        <jms:listener destination="test.queue" ref="queueReceiver1"/>
        <jms:listener destination="test.queue" ref="queueReceiver2"/>
    </jms:listener-container>

    <!-- 定义Topic监听器 -->
    <jms:listener-container destination-type="topic" container-type="default" connection-factory="connectionFactory" acknowledge="auto">
        <jms:listener destination="test.topic" ref="topicReceiver1"/>
        <jms:listener destination="test.topic" ref="topicReceiver2"/>
    </jms:listener-container>

    <!-- 消息消费者 end -->
</beans>  1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768
```

鉴于博文内容较多，我们只是在粘贴web.xml的配置，就不在博文中提供Spring和SpringMVC的XML配置，其他内容，大家查看[源码](http://download.csdn.net/detail/jiuqiyuliang/9141139)即可。

#### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    version="3.0">
    <display-name>ActiveMQSpringDemo</display-name>

    <!-- Log4J Start -->
    <context-param>
        <param-name>log4jConfigLocation</param-name>
        <param-value>classpath:log4j.properties</param-value>
    </context-param>
    <context-param>
        <param-name>log4jRefreshInterval</param-name>
        <param-value>6000</param-value>
    </context-param>
    <!-- Spring Log4J config -->
    <listener>
        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
    </listener>
    <!-- Log4J End -->

    <!-- Spring 编码过滤器 start -->
    <filter>
        <filter-name>characterEncoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!-- Spring 编码过滤器 End -->

    <!-- Spring Application Context Listener Start -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:applicationContext.xml,classpath*:ActiveMQ.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!-- Spring Application Context Listener End -->


    <!-- Spring MVC Config Start -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!-- Filter all resources -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!-- Spring MVC Config End -->

</web-app>1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071
```

### 运行效果

![这里写图片描述](http://img.blog.csdn.net/20150926151836004)

![这里写图片描述](http://img.blog.csdn.net/20150926151827047)

从上图可以看出队列模型和PUB/SUB模型的区别，Queue只能由一个消费者接收，其他Queue中的成员无法接受到被已消费的信息，而Topic则可以，只要是订阅了Topic的消费者，全部可以获取到生产者发布的信息。

### 总结

Spring提供了对JMS的支持，ActiveMQ提供了很好的实现，而此时我们已经将两者完美的结合在了一起。

下篇博文我们实现Spring和ActiveMQ消息的持久化。

[源码下载](http://download.csdn.net/detail/jiuqiyuliang/9141139)

