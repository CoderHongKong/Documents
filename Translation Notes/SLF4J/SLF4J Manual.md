# SLF4J 用户手册
The Simple Logging Facade for Java (SLF4J) ，作为各种日志框架的外观或者抽象，例如java.util.logging，logback以及log4j。SLF4J允许最终用户在部署时插入期望的日志框架。注意，你的库或者应用程序启用SLF4J意味着仅需要添加一个强制性依赖，那就是slf4j-api-1.8.0-beta2.jar。
**SNICE 1.6.0** 如果在类路径下没有发现绑定，那么SLF4J将默认无操作实现。
**SINCE 1.7.0** Logger接口中的打印方法现在提供变种接受可变参数来代替Object[].这个变化意味着SLF4J要求JDK 1.5或者更高版。在java引擎下，java编译器需要将方法里面可变参数部分转换成Object[]。 因此，通过编译器生成Logger接口在1.7.x和1.6.x版本无法区分。因此，SLF4J版本1.7.x与SLF4J版本1.6.x完全100%兼容。
**SINCE 1.7.9** 通过设置slf4j.detectLoggerNameMismatch的系统属性为true，SLF4J可以自动发现错误命名的记录器。

#### Hello World
按照编程传统中的惯例，这里是一个示例，说明通过最简单的方法使用SLF4J输出“Hello World”。首先获取一个名字为“Hello World”的记录器。该记录器又用于记录消息“Hello World”。
```
import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    public class HelloWorld {
        public static void main(String[] args) {
            Logger logger = LoggerFactory.getLogger(HelloWorld.class);
            logger.info("Hello World");
        }
    }
```
要运行示例，你首先需要下载slf4j的发行版，然后解压它。完成后，将slf4j-api-1.8.0-beta2.jar添加到类路径中。
编译和运行HelloWorld将在控制台打印如下输出结果。
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
打印此警告是因为在类路径上找不到slf4j绑定。
只要在类路径中添加绑定，警告就会消失。假如你添加slf4j-simple-1.8.0-beta2.jar以至于你的类路径包括：
- slf4j-api-1.8.0-beta2.jar
- slf4j-simple-1.8.0-beta2.jar

编译和运行HelloWorld将在控制台打印如下输出结果。
    0 [main] INFO HelloWorld - Hello World

#### 典型使用模式
下面示例代码说明了SLF4J典型的使用模式。注意：第15行使用的{}-placeholders。请参照问题 "[What is the fastest way of logging?](https://www.slf4j.org/faq.html#logging_performance)"在常见问题中获取详细信息。
```
import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    public class Wombat {
    
    final Logger logger = LoggerFactory.getLogger(Wombat.class);
    Integer t;
    Integer oldT;
    public void setTemperature(Integer temperature) {
        
            oldT = t;        
            t = temperature;
            logger.debug("Temperature set to {}. Old temperature was {}.", t, oldT);
            if(temperature.intValue() > 50) {
            logger.info("Temperature has risen above 50 degrees.");
            }
        }
    }   
```

#### 在部署的时候绑定日志框架

正如前面提到，SLF4J支持各种日志框架。SLF4J发行版附带了几个被称为“SLF4J 绑定”jar文件，每一个绑定对应一个受支持一个框架。

* * *
slf4j-log4j12-1.8.0-beta2.jar
        log4j版本1.2的绑定，一个广泛使用的日志框架。你也需要放置log4j.jar到你的类路径下。
* * *
slf4j-jdk14-1.8.0-beta2.jar
    java.util.logging的绑定，也被称为JDK1.4日志记录。
* * *
slf4j-nop-1.8.0-beta2.jar
NOP的绑定, 默默的丢弃所有日志。
* * *
slf4j-simple-1.8.0-beta2.jar
绑定简单的实现，它输出所有的事件到System.err。仅打印级别为INFO或者更高的消息。这个绑定可能在较小的应用程序上下文很有用。
* * *
slf4j-jcl-1.8.0-beta2.jar
绑定Jakarta Commons Logging。该绑定将委托所有SLF4J日志记录到JCL。
* * *
logback-classic-1.0.13.jar (requires logback-core-1.0.13.jar)
    **「本地实现」** SLF4J项目外部还有SLF4J绑定，例如：本机实现了SLF4J的logback。Logback的ch.qos.logback.classic.Logger类直接实现了org.slf4j.Logger接口。因此，使用SLF4J和logback结合使用会严格限制零内存和计算开销。

要切换日志框架，只需要替换你类路径上的slf4j绑定即可。例如：要从java.util.logging切换到log4j，只需要将slf4j-jdk14-1.8.0-beta2.jar替换成slf4j-log4j12-1.8.0-beta2.jar即可。

SLF4J不依赖任何特殊的类加载机械。事实上，每个SLF4J绑定在编译是都是硬连线的，以使用一个切只有一个特定的日志记录框架。例如，在编译时绑定slf4j-log4j12-1.8.0-beta2.jar绑定以使用log4j。在你的代码中，除了lf4j-api-1.8.0-beta2.jar之外，你只需要将你选择的一个且只有一个绑定放到相应的类路径位置。不要放置多个绑定到类路径下。以下是一般概念的图解说明。

![image](https://www.slf4j.org/images/concrete-bindings.png)

 SLF4J的接口和各种适配器非常简单。熟悉java语言的大部分开发者能在少于一小时内阅读并且完全理解代码。不需要的类加载知识，因为SLF4J没有使用也不直接访问任何类加载器。因此，SLF4J不遇到Jakarta Commons Logging (JCL)监控到的类加载问题或者内存泄漏。
   
鉴于SLF4J接口及部署模型的简单性，新日志记录框架的开发者应该能发现编写SLF4J绑定是非常简单的。

#### 库文件

广泛分布的组件和库的作者可以针对SLF4J接口进行编码，以避在其最终用户上免强加日志框架。因此，最终用户可以通过在类路径上引入相应的slf4j绑定来在部署时选择所需的日志记录框架；稍后可以通过在类路径上替换现有的绑定并且重启应用程序。实事证明，这种方法是简单而且非常稳健的。

**截止SlF4J 1.6.0版本** 如果没有发现类路径上的绑定，那么slf4j-api将默认认为没有操作实现，丢弃所以日志请求。因此，SLF4J 1.6.0及更高版本将发出绑定缺失的单个警告信息，并且继续丢弃所有日志请求而不进一步抗议，代替因为org.slf4j.impl.StaticLoggerBinder类缺失，而抛出NoClassDefFoundError。例如，让Wombat成为依赖SLF4J用于日志记录的一些生物框架。为了避免强加日志记录框架给最终用户，Wombat的发行版包括slf4j-api.jar，但没有绑定。即使类路径上缺失任何SLF4J绑定，Womabat的发行版仍然可以开箱即用，并且不应该要求最终用户从SLF4J网站下载绑定。仅当最终用户决定启用日志记录时，才需要安装与她选择的日志框架相对应的SLF4J绑定。

**基本规则嵌入组件**（例如库文件或者框架）不应该声明依赖任何SLF4J绑定，而只是依赖slf4j-api。当库声明对特定的绑定传递依赖时，该绑定将强加给最终用户，从而否定了SLF4J的目的。注意，声明对绑定的非传递依赖（例如，用于测试）不会影响最终用户。

嵌入组件中的SlF4J用法也可以在常见问题区讨论，与日志记录配置，依赖性减少和测试有关。

#### 声明日志记录的项目依赖项

鉴于Maven的传递性依赖规则，对于“常规”项目（不是库或者框架），可以使用单个依赖声明来声明日志记录依赖性。

**LOGBACK-CLASSIC** 如果你希望使用logback-classic作为底层的日志框架，你要做的就是声明"ch.qos.logback:logback-classic"作为你的pom.xml的依赖，如下所示。除了logback-classic-1.0.13.jar以外，这将拉slf4j-api-1.8.0-beta2.jar和logback-core-1.0.13.jar到你的项目中。注意，明确的声明logback-core-1.0.13 或者 slf4j-api-1.8.0-beta2.jar 依赖是无误的，并且可能需要强加Maven的“最近的定义”依赖关系中介规则来强调所述工件正确的版本。
 LOGBACK-CLASSIC 如果你希望使用logback-classic作为底层的日志框架，你要做的就是声明"ch.qos.logback:logback-classic"作为你的pom.xml的依赖，如下所示。除了logback-classic-1.0.13.jar以外，这将拉slf4j-api-1.8.0-beta2.jar和logback-core-1.0.13.jar到你的项目中。注意，明确的声明logback-core-1.0.13 或者 slf4j-api-1.8.0-beta2.jar 依赖是无误的，并且可能需要强加Maven的“最近的定义”依赖关系中介规则来强调所述工件正确的版本。
```
    <dependency> 
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.0.13</version>
    </dependency>
```
**LOG4J** 如果你希望使用log4j作为底层的日志框架，你要做的就是声明"org.slf4j:slf4j-log4j12"作为你的pom.xml的依赖，如下所示。除了slf4j-log4j12-1.8.0-beta2.jar以外，这将拉slf4j-api-1.8.0-beta2.jar和 log4j-1.2.17.jar 到你的项目中。注意，明确的声明log4j-1.2.17.jar 或者 slf4j-api-1.8.0-beta2.jar 依赖是无误的，并且可能需要强加Maven的“最近的定义”依赖关系中介规则来强调所述工件正确的版本。
```
    <dependency> 
        <groupId>org.slf4j</groupId>
         <artifactId>slf4j-log4j12</artifactId>
         <version>1.8.0-beta2</version>
    </dependency>
```
**JAVA.UTIL.LOGGING** 如果你希望使用java.util.logging作为底层的日志框架，你要做的就是声明"org.slf4j:slf4j-jdk14"作为你的pom.xml的依赖，如下所示。除了 slf4j-jdk14-1.8.0-beta2.jar 以外，这将拉slf4j-api-1.8.0-beta2.jar到你的项目中。注意，明确的声明 slf4j-api-1.8.0-beta2.jar 依赖是无误的，并且可能需要强加Maven的“最近的定义”依赖关系中介规则来强调所述工件正确的版本。
```
    <dependency> 
        <groupId>org.slf4j</groupId>
         <artifactId>slf4j-jdk14</artifactId>
         <version>1.8.0-beta2</version>
    </dependency>
```

#### 二进制兼容性

SLF4J 绑定指派一个工件，例如：slf4j-jdk14.jar 或者 slf4j-log4j12.jar 用于将slf4j绑定到底层的日志框架，例如java.util.logging 有log4j。

混合不同版本的slf4j-api.jar 和 SLF4J 绑定可能导致问题。例如，如果你正在使用slf4j-api-1.8.0-beta2.jar，那么你也应该使用slf4j-simple-1.8.0-beta2.jar，使用 slf4j-simple-1.5.5.jar 将无效。

然而，从客户端角度来看，slf4j-api的所有版本是相互兼容的。使用slf4j-api-N.jar编译的客户端代码，对于任何N和M，使用slf4j-api-M.jar都可以很好地运行。你只需要确保你的绑定版本与 slf4j-api.jar 的版本是匹配的。你不用担心项目给定依赖使用的slf4j-api.jar的版本。你随时可以使用任何版本的slf4j-api.jar，并且只要slf4j-api.jar的版本和它的绑定匹配，你应该没问题。

在初始化的时候，如果SLF4J怀疑 slf4j-api 和 其绑定存在不匹配问题，那么它将发出一个怀疑不匹配的警告。

#### 通过SLF4J合并日志

通常，给定的项目将依赖于SLF4J以外日志API的各种组件。根据JCL，java.util.logging，log4j和SLF4J的组合找到项目是很常见的。然后，需要通过单个的通道合并日志。SLF4J 迎合常见的使用场景通过提供桥接模块给 JCL, java.util.logging and log4j。有关更多详细信息，请参照 Bridging legacy APIs。

#### 映射诊断上下文支持
“映射诊断上下文支持”本质上是通过日志框架维护的映射，其中应用程序代码提供键值对，然后可以将键值对通过日志框架插入到日志信息中。MDC 数据在过滤消息或者触发某些动作也是非常有帮助的。

SLF4J支持MDC，或者映射诊断上下文。如果底层日志框架提供MDC功能，那么SLF4J将委托底层框架的MDC。此时请注意，只有log4j和logback提供MDC功能。如果底层框架不支持MDC功能，例如：java.util.logging，那么SLF4J仍然会存储MDC数据，但是其中的信息需要通过自定义的用户代码来检索。

因此，作为SLF4J用户，可以在log4j和logback的已经存在的情况下利用MDC信息，但不会将这些日志框架作为依赖项强加给用户。

更多的MDC信息，请参见 [logback手册的MDC章节](https://logback.qos.ch/manual/mdc.html)

#### 总结摘要


| 优点 | 描述 |
| --- | --- |
| 在部署项目时选择你的日志框架 | 通过在类路径上插入适当的jar文件（绑定），可以在部署是插入所需的日志记录框架 |
| 故障快速操作 | 由于JVM加载类的方式，框架绑定将在早期自动校验。如果SLF4J不能在类路径上找到绑定，它将发出一个警告信息，并且默认不执行操作。 |
| 流行日志框架的绑定 | SLF4J 支持流行的日志框架，即：log4j, java.util.logging, Simple logging 以及 NOP。logback项目原生支持 SLF4J。 |
| 桥接遗留的日志APIs | 通过SLF4J实现JCL，即：jcl-over-slf4j.jar，将允许你的项目逐步迁移至SLF4J，而不会破坏与使用JCL的现有软件的兼容性。类似地，log4j-over-slf4j.jar 和 jul-to-slf4j.jar 模块，将允许你分别将 log4j和java.util.logging 重定向到SLF4J。参考[Bridging legacy APIs](https://www.slf4j.org/legacy.html)获取更多信息。 |
| 迁移你的源代码 | slf4j迁移实用工具可以帮你迁移代码使用SLF4J。 |
| 支持参数化日志消息 | 所有的SLF4J绑定支持参数化日志，并[显着提高](https://www.slf4j.org/faq.html#logging_performance)了性能结果。 |