# [介绍](https://logback.qos.ch/manual/introduction.html)
## 1.介绍
### 1.1 什么是logback
Logback旨在作为流行的log4j项目的继任者。 它由log4j的创始人CekiGülcü设计。 它建立在十年设计工业强度测井系统的经验基础上。 由此产生的产品，即logback，速度更快，占地面积比所有现有的测井系统都要小，有时还有很大的差距。 同样重要的是，logback提供了其他日志记录系统中缺少的独特且相当实用的功能。

### 1.2 第一步
Logback-classic模块除了需要类路径上的logback-classic.jar外，还需要slf4j-api.jar和logback-core.jar。

logback - *。jar文件是logback分发的一部分，而slf4j-api-1.8.0-beta1.jar随SLF4J一起发布，这是一个单独的项目。

```
package chapters.introduction;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld1 {

  public static void main(String[] args) {

    Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld1");
    logger.debug("Hello world.");

  }
}

```

HelloWorld1类在chapter.introduction包中定义。它首先导入SLF4J API中定义的Logger和LoggerFactory类，特别是在org.slf4j包中。

在main（）方法的第一行，通过调用LoggerFactory类中的静态getLogger方法为名为logger的变量分配一个Logger实例。这个记录器被命名为“chapters.introduction.HelloWorld1”。主要方法继续调用此记录器的调试方法，以“Hello World”作为参数。我们说主要方法包含一个DEBUG级别的日志语句，其中包含消息“Hello world”。

请注意，上面的示例没有引用任何logback类。在大多数情况下，就日志而言，你的类只需要导入SLF4J类。因此，绝大多数（如果不是全部的话）你的类将使用SLF4J API，并且会忽视logback的存在。

您可以使用以下命令启动第一个示例应用程序chapter.introduction.HelloWorld1：

```
java chapters.introduction.HelloWorld1
```

启动HelloWorld1应用程序将在控制台上输出一行。 凭借logback的默认配置策略，当没有找到默认配置文件时，logback会将ConsoleAppender添加到根记录器。
```
20:49:07.962 [main] DEBUG chapters.introduction.HelloWorld1 - Hello world.
```
Logback可以使用内置的状态系统报告有关其内部状态的信息。 在logback的生命周期中发生的重要事件可以通过一个名为StatusManager的组件来访问。 目前，让我们通过调用StatusPrinter类的静态print（）方法指示logback打印其内部状态。

```
package chapters.introduction;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld2 {

  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld2");
    logger.debug("Hello world.");

    // print internal state
    LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
    StatusPrinter.print(lc);
  }
}
```
运行HelloWorld2应用程序将产生以下输出：
```
12:49:22.203 [main] DEBUG chapters.introduction.HelloWorld2 - Hello world.
12:49:22,076 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.groovy]
12:49:22,078 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
12:49:22,093 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.xml]
12:49:22,093 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Setting up default configuration.
```

Logback解释说，如果找不到logback-test.xml和logback.xml配置文件（稍后讨论），它会使用其默认策略（这是一个基本的ConsoleAppender）进行配置。 Appender是一个可以被视为输出目的地的类。 Appender存在许多不同的目的地，包括控制台，文件，Syslog，TCP套接字，JMS等等。用户还可以根据自己的具体情况轻松创建自己的Appender。

请注意，如果发生错误，logback将自动在控制台上打印其内部状态。

前面的例子很简单。在一个更大的应用程序中的实际日志记录不会有所不同日志记录的一般模式不会改变。只有配置过程会有所不同。但是，您可能需要根据需要自定义或配置logback。后续配置将在后面的章节中介绍。

请注意，在上面的示例中，我们已通过调用StatusPrinter.print（）方法指示logback打印其内部状态。 Logback的内部状态信息在诊断与登录有关的问题时非常有用。

以下列出了三个必需步骤，以便在应用程序中启用日志记录。
-  配置logback环境。 你可以用几种或多或少的复杂方式来做到这一点。 稍后再说。
-  在您希望执行日志记录的每个类中，通过调用org.slf4j.LoggerFactory类的getLogger（）方法来检索Logger实例，将当前类名称或类本身作为参数传递。
-  通过调用其打印方法（即debug（），info（），warn（）和error（）方法）来使用此记录器实例。 这将在配置的appender上产生日志输出。

### 建立logback
作为其构建工具，logback依赖于广泛使用的开源构建工具Maven。

安装完Maven之后，构建包括其所有模块的logback项目应该像在解除logback分发的目录中发出mvn install命令一样简单。 Maven会自动下载所需的外部库。

Logback分布包含完整的源代码，您可以修改部分logback库并构建您自己的版本。 只要您遵守LGPL许可证或EPL许可证的条件，您甚至可以重新分配修改后的版本。
