# spring-boot-study
## 一、spring-boot-loader(springboot启动剖析)
### 1、spring-boot应用的打包结构
![-w369](media/15750176368166.jpg)
* BOOT-INF：项目文件
* META-INF：jar包描述文件
* org：spring自定义类加载器相关的文件（为了迎合jar包规范）

### 2、spring-boot自定义类加载器
* 引入spring-boot-loader包

```dependencies {
    compile(
            "org.springframework.boot:spring-boot-starter-web",
            'org.projectlombok:lombok:1.16.8',
            'org.springframework.boot:spring-boot-loader'
    )
```

### 3、JDWP：java debug wire protocol，Java调试协议


```  Documents java -agentlib:jdwp=help
               Java Debugger JDWP Agent Library
               --------------------------------

  (see http://java.sun.com/products/jpda for more information)

jdwp usage: java -agentlib:jdwp=[help]|[<option>=<value>, ...]

Option Name and Value            Description                       Default
---------------------            -----------                       -------
suspend=y|n                      wait on startup?                  y
transport=<name>                 transport spec                    none
address=<listen/attach address>  transport spec                    ""
server=y|n                       listen for debugger?              n
launch=<command line>            run debugger on event             none
onthrow=<exception name>         debug on throw                    none
onuncaught=y|n                   debug on any uncaught?            n
timeout=<timeout value>          for listen/attach in milliseconds n
mutf8=y|n                        output modified utf-8             n
quiet=y|n                        control over terminal messages    n

Obsolete Options
----------------
strict=y|n
stdalloc=y|n

Examples
--------
  - Using sockets connect to a debugger at a specific address:
    java -agentlib:jdwp=transport=dt_socket,address=localhost:8000 ...
  - Using sockets listen for a debugger to attach:
    java -agentlib:jdwp=transport=dt_socket,server=y,suspend=y ...

Notes
-----
  - A timeout value of 0 (the default) is no timeout.

Warnings
--------
  - The older -Xrunjdwp interface can still be used, but will be removed in
    a future release, for example:
        java -Xdebug -Xrunjdwp:[help]|[<option>=<value>, ...]
```
* 本地debug spring-boot应用
```
➜  libs git:(master) java -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5050 -jar spring-study-1.0-SNAPSHOT.jar
Listening for transport dt_socket at address: 5050
```
* idea连接debug
![-w728](media/15751003600490.jpg)

### 4、启动关键类分析
1. org.springframework.core.io.support.SpringFactoriesLoader
加载"META-INF/spring.factories"文件
1. ApplicationContext接口：
2. 
```
public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch(); // 计时器，记录方法和应用执行时间
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
		configureHeadlessProperty(); // 表名是服务器端应用，没有鼠标键盘界面
		SpringApplicationRunListeners listeners = getRunListeners(args);
		listeners.starting();
		try {
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(
					args);
			ConfigurableEnvironment environment = prepareEnvironment(listeners,
					applicationArguments);
			configureIgnoreBeanInfo(environment);
			Banner printedBanner = printBanner(environment);
			context = createApplicationContext();
			exceptionReporters = getSpringFactoriesInstances(
					SpringBootExceptionReporter.class,
					new Class[] { ConfigurableApplicationContext.class }, context);
			prepareContext(context, environment, listeners, applicationArguments,
					printedBanner);
			refreshContext(context);
			afterRefresh(context, applicationArguments);
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass)
						.logStarted(getApplicationLog(), stopWatch);
			}
			listeners.started(context);
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, listeners);
			throw new IllegalStateException(ex);
		}

		try {
			listeners.running(context);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
```
## 二、spring-boot模块
### 1、spring-boot 整合kafka

* 启动zookeeper命令：
` bin/zookeeper-server-start.sh config/zookeeper.properties `
* 启动kafka命令：
`bin/kafka-server-start.sh config/server.properties `
* 创建主题：
`bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic my_topic `
* 创建生产者：
`bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my_topic`
* 创建消费者：
`bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my_topic`

### 2、kafka概念

#### 生产者（Producer)

生产者（Producer）顾名思义，生产者就是生产消息的组件，它的主要工作就是源源不断地生产出消息，然后发送给消息队列。生产者可以向消息队列发送各种类型的消息，如狭义的字符串消息，也可以发送二进制消息。生产者是消息队列的数据来源，只有通过生产者持续不断地向消息队列发送消息，消息队列才能不断地处理消息
#### 消费者（Consumer)

消费者的概念也是比较容易理解的。所谓消费者，指的是不断消费（获取）消息的组件，它获取消息的来源就是消息队列（即 Kafka 本身）。換句话说，生产者不断向消息队列发送消息，而消费者则不断从消息队列中获取消息。这里面的消息队列（即 Kafka）则充当了一个中介的角色，连接了生产者与消费者这两大功能组件。正是从这个意义上来说，借助于消息队列，我们实现了生产者系统与消费者系统之间的解耦，使得原本需要两个系统之间有紧密联系的状况変成了两个系统可以各自针对 Kafka 进行编程（只要提前约定好一些契约即可），这可以使得生产者系统完全不需要了解消费者系统的各种信息（比如说消费者系统的地址、端口号、URL、使用的是 REST 接口还是 RPC 等等；反之亦然）。这正是消息队列所提供的另外一个绝佳好处：极大降低了系统之间的耦合度。
#### 代理（Broker)
代理这个概念是消息队列领域中一个常见的概念。Broker 这个单词原本的意思是经纪人，比如说房地产经纪人、股票经纪人等。在消息队列领域中，它指的其实就是消息队列产品本身，比如说在 Kafka 这个领域下，Broker 其实指的就是一台 Kafka Server。换句话说，我们可以将部暑的一台 Kafka Server？看作是一个 Broker，就是这样简单。那么从流程上来说，生产者会将消息发送给 Broker，然后消费者再从 Brokerp 中拉取消息。

#### 主题（Topic）

主题是 Kafka 中一个极为重要的概念。首先，主题是一个逻辑上的概念，它用于从逻辑上来归类与存储消息本身。多个生产者可以向一个 Topic 发送消息，同时也可以有多个消费者消费一个 Topice 中的消息。Topic 还有分区与副本的概念，这两个概念我们将会在后续课程中遇到时详细介绍。现在，你需要理解的是，Topic 与消息这两个概念之间密切相关，Kafka 中的每一条消息都会归属于某一个 Topic，而一个 Topic 下面可以有任意数量的消息。正是借助于 opic 这个逻辑上的概念，Kafka 将各种各样的消息进行了分门别类，使得不同的消息归属于不同的 Topic，这样就可以很好地实现不同系统的生产者可以向同一个 Broker 发送消息，而不同系统的消费者则可以根据 Topic 的名字从 Brokerl 中拉取消息。Topici 是一个字符串。实际上，在上节课中，生产者发送消息时就指定了将消息发送给哪个 Topic，在那个示例中，我们将消息发送给了名为『mytest！的主题，而消费者在拉取消息时也指定了拉取 Topic 名为 mytest』的消息。通过 Topic 这样一个逻辑上的概念，我们就很好地实现了生产者与消费者之间有针对性的发送与拉取。

#### 消息（Record）

消息是整个消息队列中最为基本的一个概念，也是最为原子的一个概念。它指的是生产者发送与消费者拉取的一个原子事物。一个消息需要关联到一个 Topic 上，表示该消息从属于哪个 Topic。消息由一串字节所构成，其中主要由 key 和 value 两部分内容，key 与 value 本质上都是字节数组。在发送消息时，我们可以省略掉 key 部分，而直接使用 valuei 部分。正如上一节的示例那样，生产者在发送消息时，发送的內容是 『Thello world』、『welcome』与『见到你很高兴』。实际上，他们都是消息的 value，即消息真正的内容本身；**key 的主要作用则是根据一定的策略，将此消息发送到指定的分区中，这样就可以确保包含同一key值的消息全部都写入到同一个分区中**。因此，我们可以得出这样一个结论：对于 Kafka 的消息来说，真正的消息内容本身是由 value 所承载的。为了提升消息发送的效率和存储效率，生产者会批量将消息发送给 Broker，并根据相应的压缩算法在发送前对消息进行压缩。

#### 控制器（Controller)

控制器是集群中的概念。每个集群中会选择出一个 Brokerf 担任控制器的角色，控制器是 Kafka 集群的中心。一个 Kafka 集群中，控制器这台 Broker 之外的其他 Brokers 会根据控制器的指挥来实现相应的功能。控制器负责管理 Kafka'分区的状态、管理每个分区的副本状态、监听 Zoo Keeper 中数据的变化并作出相应的反馈等功能。此外，控制器也类似于主从的概念（比如说 MSL 的主从概念），所有的 Broker 都会监听控制器 Leader 的状态，当 Leader 控制器出现问题或是故障时则重新选择新的控制器 Leader，这里面涉及到选举算法的问题。

##### 消费者组（Consumer Group)

这又是 Kafka 中的一个核心概念。消费者组与消费者之间密切相关。在 Kafka 中，多个消费者可以共同构成一个消费者组，而一个消费者只能从属于一个消费者组。消费者组最为重要的一个功能是实现广播与单播的功能。一个消费者组可以确保其所订阅的 Topic 的每个分区只能被从属于该消费者组中的唯一一个消费者所消费；如果不同的消费者组订阅了同个 Topic，那么这些消费者组之间是彼此独立的，不会受到相互的干扰。因此，如果我们希望一条消息可以被多个消费者所消费，那就可以将这些消费者放置到不同的消费者组中这实际上就是**广播**的效果；如果希望一条消息只能被一个消费者所消费，那么就可以将这些消费者放置到同一个消费者组中，这实际上就是**单播**的效果。因此，我们可以将消费者组看作是『逻辑上的订阅者』，而物理上的订阅者则是各个消费者。值得注意的是，消费者组是一个非常、非常、非常重要的概念。很多 Kafka 初学者都会遇到这样一个问题：将系统以集群的形式部署（比如说部署到 3 台机器或是虚拟机上），每台机器的指定代码都是完全一样的，那么在运行时，只会有一台机器会持续不断地收到 Brokerr 中的消息，而其他机器则一条消息也收不到。究其本质，系统部署时采用了集群部署，因此每台机器的代码与配置都是完全一样的；这样，这些机器（消费者）都从属于同一个消费者组，既然从属于同一个消费者组，那么这同一个消费者组中，只会有一个消费者会接收到消息，而其他消费者则完全接收不到任何消息，即单播的效果。这一点尤其值得大家注意。

#### 