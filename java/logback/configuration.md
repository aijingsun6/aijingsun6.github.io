# [configuration](https://logback.qos.ch/manual/configuration.html)
## 3. Logback配置
我们首先介绍配置logback的方法，以及许多示例配置脚本。 Joran，logback依赖的配置框架将在后面的章节中介绍。

### 在logback中进行配置
将日志请求插入到应用程序代码中需要进行相当多的计划和努力。 观察显示，大约百分之四的代码专用于日志记录。 因此，即使是中等大小的应用程序也会包含嵌入代码中的数千条日志语句。 鉴于他们的人数，我们需要工具来管理这些日志语句。

Logback可以以编程方式配置，也可以使用XML或Groovy格式表示的配置脚本进行配置。 顺便说一句，现有的log4j用户可以使用我们的PropertiesTranslator网络应用程序将他们的log4j.properties文件转换为logback.xml。

让我们从讨论logback的初始化步骤开始，尝试配置它自己：
-  Logback试图在类路径中找到名为logback-test.xml的文件。
-  如果找不到这样的文件，logback会尝试在类路径中找到名为logback.groovy的文件。
-  如果找不到这样的文件，它会检查类路径中的文件logback.xml。
-  如果找不到这样的文件，则通过查找文件META-INF \ services \ ch来使用服务提供者加载工具（在JDK 1.6中引入）来解析com.qos.logback.classic.spi.Configurator接口的实现。 类路径中的qos.logback.classic.spi.Configurator。 其内容应指定所需配置器实现的完全限定类名称。

如果以上都不成功，则使用BasicConfigurator自动配置自身，这将导致日志输出被定向到控制台。
最后一步是在没有配置文件的情况下提供默认（但非常基本的）日志功能的最后努力。

如果您使用的是Maven，并且如果将logback-test.xml放在src / test / resources文件夹下，Maven将确保它不会包含在生成的工件中。 因此，您可以使用其他配置文件，即测试期间的logback-test.xml和生产中的另一个文件（即logback.xml）。

快速启动Joran解析给定的logback配置文件大约需要100毫秒。 要在应用程序启动时削减这些毫秒数，可以使用服务提供程序加载工具（上面的第4项）来加载您自己的定制配置程序类并使用BasicConfigrator作为一个很好的起点。

### 自动配置登录
配置logback的最简单方法是让logback回退到默认配置。 让我们尝试一下如何在一个名为MyApp1的虚拟应用程序中完成这项工作。

```
package chapters.configuration;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyApp1 {
  final static Logger logger = LoggerFactory.getLogger(MyApp1.class);

  public static void main(String[] args) {
    logger.info("Entering application.");

    Foo foo = new Foo();
    foo.doIt();
    logger.info("Exiting application.");
  }
}
```
这个类定义了一个静态记录器变量。 然后它实例化一个Foo对象。 Foo类如下所示：

```
package chapters.configuration;
  
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
   
public class Foo {
  static final Logger logger = LoggerFactory.getLogger(Foo.class);
  
  public void doIt() {
    logger.debug("Did it again!");
  }
}
```
为了运行本章中的示例，您需要确保某些jar文件存在于类路径中。 请参阅设置页面了解更多详情。

假设配置文件logback-test.xml或logback.xml不存在，logback将默认调用BasicConfigurator，它将设置最小配置。 这个最小配置包含一个连接到根记录器的ConsoleAppender。 输出使用PatternLayoutEncoder设置为格式％d {HH：mm：ss.SSS} [％thread]％-5level％logger {36} - ％msg％n。 而且，默认情况下，根记录器被分配了DEBUG级别。

因此，命令java chapters.configuration.MyApp1的输出应该类似于：

```
16:06:09.031 [main] INFO  chapters.configuration.MyApp1 - Entering application.
16:06:09.046 [main] DEBUG chapters.configuration.Foo - Did it again!
16:06:09.046 [main] INFO  chapters.configuration.MyApp1 - Exiting application.
```

MyApp1应用程序通过对org.slf4j.LoggerFactory和org.slf4j.Logger类的调用链接到logback，检索它希望使用的记录器，然后继续。 请注意，Foo类在logback上的唯一依赖关系是通过org.slf4j.LoggerFactory和org.slf4j.Logger导入。 除了配置logback的代码（如果存在这样的代码），客户端代码不需要依赖于logback。 由于SLF4J允许在其抽象层下使用任何日志框架，所以很容易将大量代码从一个日志框架迁移到另一个日志框架。

### 使用logback-test.xml或logback.xml自动配置
如前所述，如果在类路径中找到logback，它将尝试使用文件logback-test.xml或logback.xml进行自我配置。 这是一个相当于我们刚刚看到的BasicConfigurator建立的配置文件。

```
file:sample0.xml
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

将sample0.xml重命名为logback.xml（或logback-test.xml）后，将其放入可从类路径访问的目录中。 运行MyApp1应用程序应该可以得到与之前的运行相同的结果。

### 在出现警告或错误时自动打印状态消息
如果在解析配置文件过程中出现警告或错误，logback将自动在控制台上打印其内部状态数据。 请注意，为避免重复，如果用户明确注册状态监听器（以下定义），则禁用自动状态打印。

在没有警告或错误的情况下，如果您仍然希望检查logback的内部状态，则可以通过调用StatusPrinter类的print（）指示logback打印状态数据。 下面显示的MyApp2应用程序与MyApp1完全相同，只是增加了两行代码来打印内部状态数据。

```
public static void main(String[] args) {
  // assume SLF4J is bound to logback in the current environment
  LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
  // print logback's internal status
  StatusPrinter.print(lc);
  ...
}
```
如果一切顺利，您应该在控制台上看到以下输出
```
17:44:58,578 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Found resource [logback-test.xml]
17:44:58,671 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - debug attribute not set
17:44:58,671 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.ConsoleAppender]
17:44:58,687 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [STDOUT]
17:44:58,812 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Popping appender named [STDOUT] from the object stack
17:44:58,812 |-INFO in ch.qos.logback.classic.joran.action.LevelAction - root level set to DEBUG
17:44:58,812 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [STDOUT] to Logger[root]

17:44:58.828 [main] INFO  chapters.configuration.MyApp2 - Entering application.
17:44:58.828 [main] DEBUG chapters.configuration.Foo - Did it again!
17:44:58.828 [main] INFO  chapters.configuration.MyApp2 - Exiting application.
```
在输出结束时，您可以识别上一个示例中打印的行。 您还应该注意到logback的内部消息，a.k.a.状态对象，它允许方便地访问logback的内部状态。
### 状态数据
不用从代码中以编程方式调用StatusPrinter，即使没有错误，也可以指示配置文件转储状态数据。 为此，需要设置配置元素的调试属性，即配置文件中最顶层的元素，如下所示。 请注意，此调试属性仅与状态数据相关。 它不会影响logback的配置，特别是在记录器级别方面。 （如果您问，否，根记录器将不会设置为DEBUG。）

```
file:sample1.xml
<configuration debug="true"> 

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
    <!-- encoders are  by default assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder -->
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>

or
//
// Built on Sun Apr 15 17:33:43 CEST 2018 by logback-translator
// For more information on configuration files in Groovy
// please see http://logback.qos.ch/manual/groovy.html

// For assistance related to this tool or configuration files
// in general, please contact the logback user mailing list at
//    http://qos.ch/mailman/listinfo/logback-user

// For professional support please see
//   http://www.qos.ch/shop/products/professionalSupport

import ch.qos.logback.classic.encoder.PatternLayoutEncoder
import ch.qos.logback.core.ConsoleAppender

import static ch.qos.logback.classic.Level.DEBUG

appender("STDOUT", ConsoleAppender) {
  encoder(PatternLayoutEncoder) {
    pattern = "%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"
  }
}
root(DEBUG, ["STDOUT"])
```
在<configuration>元素中设置debug =“true”将会输出状态信息，假设：
-  找到配置文件
-  配置文件是格式良好的XML。

如果这两个条件中的任何一个没有满足，Joran就无法解释调试属性，因为配置文件无法读取。 如果找到配置文件但格式不正确，则logback将检测到错误情况并自动在控制台上打印其内部状态。 但是，如果找不到配置文件，logback将不会自动打印其状态数据，因为这不一定是错误情况。 以编程方式调用StatusPrinter.print（）（如上面的MyApp2应用程序中所示）可确保在每种情况下都打印状态信息。

在没有状态消息的情况下，追踪恶意logback.xml配置文件可能很困难，尤其是在应用程序源不易修改的生产环境中。 为帮助识别恶意配置文件的位置，可以通过“logback.statusListenerClass”系统属性（定义如下）设置StatusListener以强制输出状态消息。 “logback.statusListenerClass”系统属性也可用于在发生错误时自动生成输出的静音。

顺便说一下，设置debug =“true”严格等同于安装OnConsoleStatusListener。 状态监听器在下面进一步讨论。 接下来会显示OnConsoleStatusListener的安装。

###### src/main/resources/chapters/configuration/onConsoleStatusListener.xml
```
<configuration>
  <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />  

  ... the rest of the configuration file  
</configuration>
```
通过调试属性或等效地安装OnConsoleStatusListener启用状态数据输出，将有助于诊断Logback问题。 因此，强烈建议启用logback状态数据，并应将其视为第一手段的追索权。

### 指定默认配置文件的位置作为系统属性
您可以使用名为“logback.configurationFile”的系统属性指定默认配置文件的位置。 此属性的值可以是URL，类路径上的资源或应用程序外部文件的路径。

``` java -Dlogback.configurationFile=/path/to/config.xml chapters.configuration.MyApp1 ```
请注意，文件扩展名必须是“.xml”或“.groovy”。 其他扩展名被忽略。 显式注册状态侦听器可能有助于调试定位配置文件的问题。

鉴于“logback.configurationFile”是一个Java系统属性，它也可以在你的应用程序中设置。 但是，必须在创建任何记录器实例之前设置系统属性。
```
import ch.qos.logback.classic.util.ContextInitializer;

public class ServerMain {
    public static void main(String args[]) throws IOException, InterruptedException {
       // must be set before the first call to  LoggerFactory.getLogger();
       // ContextInitializer.CONFIG_FILE_PROPERTY is set to "logback.configurationFile"
       System.setProperty(ContextInitializer.CONFIG_FILE_PROPERTY, /path/to/config.xml);
       ...
    }   
}
```
###修改后自动重新加载配置文件
如果指示这样做，logback-classic将扫描其配置文件中的更改并在配置文件更改时自动重新配置自身。 为了指示logback-classic扫描其配置文件中的更改并自动重新配置自身，请将<configuration>元素的扫描属性设置为true，如下所示。

###### src/main/resources/chapters/configuration/scan1.xml
```
<configuration scan="true"> 
  ... 
</configuration> 
or
//
// Built on Sun Apr 15 17:41:15 CEST 2018 by logback-translator
// For more information on configuration files in Groovy
// please see http://logback.qos.ch/manual/groovy.html

// For assistance related to this tool or configuration files
// in general, please contact the logback user mailing list at
//    http://qos.ch/mailman/listinfo/logback-user

// For professional support please see
//   http://www.qos.ch/shop/products/professionalSupport

scan()
```
默认情况下，配置文件将每分钟扫描一次更改。 您可以通过设置<configuration>元素的scanPeriod属性来指定不同的扫描周期。 值可以以毫秒，秒，分钟或小时为单位指定。 这是一个例子

##### src/main/resources/chapters/configuration/scan2.xml
```
xml:
<configuration scan="true" scanPeriod="30 seconds" > 
  ...
</configuration> 
or
groovy:
scan("30 seconds")
```

如果没有指定时间单位，则假定时间单位是毫秒，这通常是不合适的。 如果您更改默认的扫描周期，请不要忘记指定一个时间单位。

在幕后，当您将scan属性设置为true时，将会安装ReconfigureOnChangeTask。 此任务在单独的线程中运行，并将检查您的配置文件是否已更改。 ReconfigureOnChangeTask也会自动监视任何包含的文件。

由于编辑配置文件时发生错误很容易，如果最新版本的配置文件具有XML语法错误，它将回退到以前的配置文件，而没有XML语法错误。

### 在堆栈跟踪中启用打包数据
如果指示这样做，logback可以包括它输出的堆栈跟踪线的每一行的打包数据。 打包数据由栈文件的名称和版本组成，其中源自堆栈跟踪行的类。 打包数据在识别软件版本问题时非常有用。 但是，计算起来相当昂贵，特别是在频繁抛出异常的应用程序中。 这里是一个示例输出：

```
14:28:48.835 [btpool0-7] INFO  c.q.l.demo.prime.PrimeAction - 99 is not a valid value
java.lang.Exception: 99 is invalid
  at ch.qos.logback.demo.prime.PrimeAction.execute(PrimeAction.java:28) [classes/:na]
  at org.apache.struts.action.RequestProcessor.processActionPerform(RequestProcessor.java:431) [struts-1.2.9.jar:1.2.9]
  at org.apache.struts.action.RequestProcessor.process(RequestProcessor.java:236) [struts-1.2.9.jar:1.2.9]
  at org.apache.struts.action.ActionServlet.doPost(ActionServlet.java:432) [struts-1.2.9.jar:1.2.9]
  at javax.servlet.http.HttpServlet.service(HttpServlet.java:820) [servlet-api-2.5-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.servlet.ServletHolder.handle(ServletHolder.java:502) [jetty-6.1.12.jar:6.1.12]
  at ch.qos.logback.demo.UserServletFilter.doFilter(UserServletFilter.java:44) [classes/:na]
  at org.mortbay.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1115) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.servlet.ServletHandler.handle(ServletHandler.java:361) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.webapp.WebAppContext.handle(WebAppContext.java:417) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.handler.ContextHandlerCollection.handle(ContextHandlerCollection.java:230) [jetty-6.1.12.jar:6.1.12]
```
打包数据默认是禁用的，但可以通过配置启用：
```
<configuration packagingData="true">
  ...
</configuration>
```
或者，可以通过调用LoggerContext中的setPackagingDataEnabled（boolean）方法以编程方式启用/禁用打包数据，如下所示：

```
LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
  lc.setPackagingDataEnabled(true);
```
### 直接调用JoranConfigurator
Logback依赖于称为Joran的配置库，它是logback-core的一部分。 Logback的默认配置机制在它在类路径上找到的默认配置文件上调用JoranConfigurator。 如果您希望无论出于何种原因覆盖logback的默认配置机制，可以直接调用JoranConfigurator来实现。 下一个应用程序MyApp3在作为参数传递的配置文件上调用JoranConfigurator。

```
package chapters.configuration;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.joran.JoranConfigurator;
import ch.qos.logback.core.joran.spi.JoranException;
import ch.qos.logback.core.util.StatusPrinter;

public class MyApp3 {
  final static Logger logger = LoggerFactory.getLogger(MyApp3.class);

  public static void main(String[] args) {
    // assume SLF4J is bound to logback in the current environment
    LoggerContext context = (LoggerContext) LoggerFactory.getILoggerFactory();
    
    try {
      JoranConfigurator configurator = new JoranConfigurator();
      configurator.setContext(context);
      // Call context.reset() to clear any previous configuration, e.g. default 
      // configuration. For multi-step configuration, omit calling context.reset().
      context.reset(); 
      configurator.doConfigure(args[0]);
    } catch (JoranException je) {
      // StatusPrinter will handle this
    }
    StatusPrinter.printInCaseOfErrorsOrWarnings(context);

    logger.info("Entering application.");

    Foo foo = new Foo();
    foo.doIt();
    logger.info("Exiting application.");
  }
}
```
此应用程序获取当前有效的LoggerContext，创建一个新的JoranConfigurator，设置其操作的上下文，重置记录器上下文，然后最终要求配置器使用作为参数传递给应用程序的配置文件配置上下文。 出现警告或错误时打印内部状态数据。 请注意，对于多步骤配置，应该省略context.reset（）调用。

### 查看状态消息
Logback将其内部状态数据收集到StatusManager对象中，可通过LoggerContext访问。

给定一个StatusManager，你可以访问所有与logback上下文相关的状态数据。 为了将内存使用保持在合理的水平，默认的StatusManager实现将状态消息存储在两个单独的部分中：头部部分和尾部部分。 标题部分存储第一个H状态消息，而尾部分存储最后的T个消息。 目前时间H = T = 150，尽管这些值在未来版本中可能会有所变化。

Logback-经典的船舶，带有一个名为ViewStatusMessagesServlet的servlet。 该servlet将与当前LoggerContext关联的StatusManager的内容打印为HTML表格。 这里是示例输出。

要将此servlet添加到您的Web应用程序，请将以下行添加到其WEB-INF / web.xml文件中。
```
<servlet>
    <servlet-name>ViewStatusMessages</servlet-name>
    <servlet-class>ch.qos.logback.classic.ViewStatusMessagesServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>ViewStatusMessages</servlet-name>
    <url-pattern>/lbClassicStatus</url-pattern>
  </servlet-mapping>
```
### 收听状态消息
您还可以将StatusListener附加到StatusManager，以便您可以立即采取措施来响应状态消息，特别是在进行logback配置后发生的消息。 注册状态监听器是一种监督logback内部状态的便捷方式，无需人工干预。

Logback附带一个名为OnConsoleStatusListener的StatusListener实现，它的名称表示它将在控制台上打印所有新的传入状态消息。

以下是使用StatusManager注册OnConsoleStatusListener实例的示例代码。

```
LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory(); 
   StatusManager statusManager = lc.getStatusManager();
   OnConsoleStatusListener onConsoleListener = new OnConsoleStatusListener();
   statusManager.add(onConsoleListener);
```
请注意，注册状态监听器只会在注册后接收状态事件。 它不会收到先前的消息。 因此，在其他指令之前将状态监听器注册指令放在配置文件的顶部通常是一个好主意。

##### src/main/resources/chapters/configuration/onConsoleStatusListener.xml
```
<configuration>
  <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />  

  ... the rest of the configuration file  
</configuration>
```

### logback.statusListenerClass 系统属性
也可以通过将“logback.statusListenerClass”Java系统属性设置为您希望注册的侦听器类的名称来注册状态侦听器。 例如，

```
java -Dlogback.statusListenerClass=ch.qos.logback.core.status.OnConsoleStatusListener...
```
Logback附带有几个状态监听器实现。 OnConsoleStatusListener在控制台上打印传入的状态消息，即在System.out上。 OnErrorConsoleStatusListener在System.err上打印传入的状态消息。 NopStatusListener丢弃传入的状态消息。

请注意，如果在配置期间注册了任何状态侦听器，并且特别是在用户通过“logback.statusListenerClass”系统指定了状态侦听器时，自动状态打印（出现错误时）将被禁用。 因此，通过将NopStatusListener设置为状态监听器，您可以完全禁止内部状态打印。

```
java -Dlogback.statusListenerClass=ch.qos.logback.core.status.NopStatusListener 
```
### 停止logback-classic
为了释放logback-classic使用的资源，停止logback上下文总是一个好主意。 停止上下文将关闭所有附加到由上下文定义的记录器的appender，并以有序的方式停止任何活动线程。 另请阅读下面关于“关闭挂钩”的部分。

```
import org.sflf4j.LoggerFactory;
import ch.qos.logback.classic.LoggerContext;
...

// assume SLF4J is bound to logback-classic in the current environment
LoggerContext loggerContext = (LoggerContext) LoggerFactory.getILoggerFactory();
loggerContext.stop();
```
在web应用程序中，可以从ServletContextListener的contextDestroyed方法中调用上述代码，以便停止logback-经典和释放资源。 从版本1.1.10开始，适当的ServletContextListener会自动为您安装


### 通过关机挂钩停止logback-classic
安装JVM关闭挂钩是关闭logback并释放相关资源的一种便捷方式。

```
<configuration debug="true">
   <!-- in the absence of the class attribute, assume 
   ch.qos.logback.core.hook.DefaultShutdownHook -->
   <shutdownHook/>
  .... 
</configuration>
```
请注意，您可以通过将class属性设置为与您的关闭钩子的类名相对应来安装自己制作的关闭钩子。

默认的关闭挂钩，即DefaultShutdownHook，将在指定的延迟（默认为0）后停止logback上下文。 停止上下文将允许在后台运行的任何日志文件压缩任务最多持续30秒完成。 在独立的Java应用程序中，向配置文件添加<shutdownHook />指令是确保任何正在进行的压缩任务都可以在JVM退出前完成的简单方法。 在Web服务器中的应用程序中，webShutdownHook会自动安装，使得<shutdownHook />指令非常多且不必要。

### WebShutdown挂钩或停止web应用程序中的logback-classic
logback-classic会自动要求Web服务器安装实现ServletContainerInitializer接口的LogbackServletContainerInitializer（在servlet-api 3.x和更高版本中可用）。 这个初始化器将依次安装LogbackServletContextListener的实例。 该监听器将停止或重新加载Web应用程序时停止当前的logback-classic上下文。

您可以通过在Web应用程序的web.xml文件中设置名为logbackDisableServletContainerInitializer的<context-param>来禁用自动安装LogbackServletContextListener。 这是相关的片段。

```
<web-app>
    <context-param>
        <param-name>logbackDisableServletContainerInitializer</param-name>
        <param-value>true</param-value>
    </context-param>
    .... 
</web-app>
```
请注意，logbackDisableServletContainerInitializer变量也可以设置为Java系统属性，即OS环境变量。 最本地的设置具有优先权，即首先是网络应用程序，系统属性第二和操作系统环境最后。


### 配置文件语法
正如您在手册中所看到的，还有大量示例仍然遵循，logback允许您重新定义日志记录行为，而无需重新编译代码。事实上，您可以轻松配置logback，以便禁用应用程序某些部分的日志记录，或将输出直接输出到UNIX Syslog守护程序，数据库，日志可视化器或将日志记录事件转发到远程Logback服务器，根据本地服务器策略，例如通过将日志事件转发到第二个Logback服务器。

本节的其余部分介绍配置文件的语法。

正如一遍又一遍地演示的那样，logback配置文件的语法非常灵活。因此，使用DTD文件或XML模式指定允许的语法是不可能的。尽管如此，配置文件的基本结构可以描述为<configuration>元素，其中包含零个或多个<appender>元素，后跟零个或多个<logger>元素，后跟最多一个<root>元素。下图说明了这个基本结构。

#### 标签名称的大小写敏感性
自logback版本0.9.17以来，与显式规则有关的标签名称不区分大小写。 例如，<logger>，<Logger>和<LOGGER>是有效的配置元素，并且将以相同的方式进行解释。 请注意，XML格式规则仍然适用，如果您打开<xyz>标签，则必须将其关闭为</ xyz>，</ xyZ>不起作用。 至于隐式规则，除第一个字母外，标签名称区分大小写。 因此，<xyz>和<Xyz>是等价的，但不是<xYz>。 隐式规则通常遵循Java世界中常见的camelCase约定。 由于不容易判断某个标签何时与某个显式操作关联，何时该显式操作与某个隐式操作关联，因此说明XML标签对于第一个字母是区分大小写还是不敏感。 如果您不确定给定标签名称使用哪种情况，只需遵循几乎总是正确的惯例的camelCase约定。


#### 配置记录器或<logger>元素
此时，您至少应该对层次继承和基本选择规则有所了解。 否则，除非你是一位埃及学者，否则logback配置对你来说不会比象形文字更有意义。

记录器使用<logger>元素进行配置。 一个<logger>元素只需要一个强制名称属性，一个可选的级别属性和一个可选的additivity属性，允许值为true或false。 level属性的值允许输入一个不区分大小写的字符串值TRACE，DEBUG，INFO，WARN，ERROR，ALL或OFF。 不区分大小写的特殊值INHERITED或其同义词NULL将强制记录器的层次从层次结构中的较高层继承。 如果您设置记录器的级别并稍后决定它应该继承其级别，那么这很方便。

<logger>元素可能包含零个或多个<appender-ref>元素; 由此引用的每个appender被添加到指定的记录器。 请注意，与log4j不同，在配置给定的记录器时，logback-classic不会关闭也不会移除任何先前引用的appender。

### 配置根记录器或<root>元素
<root>元素配置根记录器。 它支持一个属性，即level属性。 它不允许任何其他属性，因为可加性标志不适用于根记录器。 此外，由于根记录器已被命名为“ROOT”，因此它也不允许使用名称属性。 level属性的值可以是不区分大小写的字符串TRACE，DEBUG，INFO，WARN，ERROR，ALL或OFF之一。 请注意，根记录器的级别不能设置为INHERITED或NULL。

与<logger>元素类似，<root>元素可以包含零个或多个<appender-ref>元素; 由此引用的每个appender都被添加到根记录器。 请注意，与log4j不同，在配置根记录器时，logback-classic不会关闭也不会移除任何先前引用的appender。

### Example
如下例所示，设置记录器或根记录器的级别非常简单，只需声明它并设置其级别即可。 假设我们不再有兴趣看到来自属于“chapters.configuration”包的任何组件的任何DEBUG消息。 以下配置文件显示了如何实现这一点。

##### src/main/resources/chapters/configuration/sample2.xml
```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration" level="INFO"/>

  <!-- Strictly speaking, the level attribute is not necessary since -->
  <!-- the level of the root level is set to DEBUG by default.       -->
  <root level="DEBUG">          
    <appender-ref ref="STDOUT" />
  </root>  
  
</configuration>
```
当上述配置文件作为MyApp3应用程序的参数提供时，它将产生以下输出：

```
17:34:07.578 [main] INFO  chapters.configuration.MyApp3 - Entering application.
17:34:07.578 [main] INFO  chapters.configuration.MyApp3 - Exiting application.
```
请注意，由“chapters.configuration.Foo”记录器生成的级别DEBUG消息已被抑制。 另请参阅Foo类。

您可以根据需要配置尽可能多的记录器的级别。 在下一个配置文件中，我们将章节配置记录器的级别设置为INFO，但同时将章节.configuration.Foo记录器的级别设置为DEBUG。

##### 设置多个记录器的级别 src/main/resources/chapters/configuration/sample3.xml
```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>
        %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
     </pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration" level="INFO" />
  <logger name="chapters.configuration.Foo" level="DEBUG" />

  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>

</configuration>
```
使用此配置文件运行MyApp3将在控制台上生成以下输出：
```
17:39:27.593 [main] INFO  chapters.configuration.MyApp3 - Entering application.
17:39:27.593 [main] DEBUG chapters.configuration.Foo - Did it again!
17:39:27.593 [main] INFO  chapters.configuration.MyApp3 - Exiting application.
```

由此可见，MyApp3类中的INFO级别的两条日志语句以及Foo.doIt（）中的DEBUG消息都已启用。 请注意，根记录器的级别始终设置为非空值，默认情况下为DEBUG。

让我们注意到，基本选择规则取决于被调用的记录器的有效等级，而不是附加器附加的记录器的等级。 Logback将首先确定是否启用日志记录语句，如果启用，它将调用记录器层次结构中找到的appender，而不管其级别如何。 配置文件sample4.xml就是一个例子：

##### src/main/resources/chapters/configuration/sample4.xml
```
<configuration>

  <appender name="STDOUT"
   class="ch.qos.logback.core.ConsoleAppender">
   <encoder>
     <pattern>
        %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
      </pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration" level="INFO" />

  <!-- turn OFF all logging (children can override) -->
  <root level="OFF">
    <appender-ref ref="STDOUT" />
  </root>

</configuration>
```
命名为STDOUT的ConsoleAppender是sample4.xml中唯一配置的appender，与级别设置为OFF的根记录器相连。 但是，使用配置脚本sample4.xml运行MyApp3将产生：
```
17:52:23.609 [main] INFO chapters.configuration.MyApp3 - Entering application.
17:52:23.609 [main] INFO chapters.configuration.MyApp3 - Exiting application.
```
因此，根记录器的级别没有明显影响，因为配置.MyApp3和chap.configuration.Foo类中的记录器都启用了INFO级别。 作为一个方面说明，章节。配置记录器凭借其在配置文件中的声明而存在 - 即使Java源代码不直接引用它。

### 配置Appender
appender使用<appender>元素进行配置，该元素具有两个强制属性名称和类别。 name属性指定appender的名称，而class属性指定要实例化的appender类的完全限定名称。 <appender>元素可以包含零个或一个<layout>元素，零个或多个<encoder>元素以及零个或多个<filter>元素。 除了这三个常见元素之外，<appender>元素可以包含与appender类的JavaBean属性相对应的任意数量的元素。 无缝支持给定logback组件的任何属性是Joran的主要优点之一，正如后面章节中讨论的。 下图说明了通用结构。 请注意，对属性的支持不可见。

<layout>元素采用强制类属性来指定要实例化的布局类的完全限定名称。 与<appender>元素一样，<layout>可以包含与布局实例的属性相对应的其他元素。 由于这是常见的情况，如果布局类是PatternLayout，那么类属性可以省略，如默认类映射规则所指定的。

<编码器>元素采用强制类属性来指定要实例化的编码器类的完全限定名称。 由于这是一种常见的情况，如果编码器类是PatternLayoutEncoder，那么class属性可以省略，如默认类映射规则所指定的。

记录到多个appender就像定义各种appender并在记录器中引用它们一样简单，如下面的配置文件所示：


##### Multiple loggers(src/main/resources/chapters/configuration/multiple.xml)
```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>myApp.log</file>

    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
    <appender-ref ref="STDOUT" />
  </root>

</configuration>
```

这些配置脚本定义了两个名为FILE和STDOUT的appender。 FILE appender记录到名为myApp.log的文件。 此appender的编码器是一个PatternLayoutEncoder，用于输出日志请求所在的日期，级别，线程名称，记录器名称，文件名和行号，消息和行分隔符。 称为STDOUT的第二个appender输出到控制台。 此appender的编码器仅输出消息字符串，后跟一个行分隔符。

appender通过在appender-ref元素内引用它们来连接到根记录器。 请注意，每个appender都有自己的编码器。 编码器通常不会被多个appender共享。 布局也是如此。 因此，logback配置文件不提供共享编码器或布局的任何语法手段。

### Appenders积累
默认情况下，appender是累积的：记录器将记录到附属于自身的appender（如果有的话）以及所有附属于其祖先的appender。 因此，将同一appender附加到多个记录器会导致记录输出重复。

##### 重复的appender src/main/resources/chapters/configuration/duplicate.xml
```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration">
    <appender-ref ref="STDOUT" />
  </logger>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
使用duplicate.xml运行MyApp3将生成以下输出：
```
14:25:36.343 [main] INFO  chapters.configuration.MyApp3 - Entering application.
14:25:36.343 [main] INFO  chapters.configuration.MyApp3 - Entering application.
14:25:36.359 [main] DEBUG chapters.configuration.Foo - Did it again!
14:25:36.359 [main] DEBUG chapters.configuration.Foo - Did it again!
14:25:36.359 [main] INFO  chapters.configuration.MyApp3 - Exiting application.
14:25:36.359 [main] INFO  chapters.configuration.MyApp3 - Exiting application.
```
注意重复的输出。 名为STDOUT的appender附加到两个记录器，以root和chapters.configuration。 由于根记录器是所有记录器和章节的祖先，配置是章节configuration.MyApp3和chapters.configuration.Foo的父项，因此使用这两个记录器进行的每个记录请求都将输出两次，一次是因为STDOUT被附加到 章节。配置和一次，因为它附加到根。

Appender的可加性并不是为新用户设置的陷阱。 这是一个非常方便的登录功能。 例如，您可以配置日志记录，以便日志消息显示在控制台上（对于系统中的所有日志记录器），而仅来自某些特定记录器集合的消息流入特定的appender。

### 多个appender src/main/resources/chapters/configuration/restricted.xml
```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>myApp.log</file>
    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration">
    <appender-ref ref="FILE" />
  </logger>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
在本例中，控制台appender将记录所有消息（针对系统中的所有记录器），而只记录源自chapters.configuration记录器及其子项的请求将进入myApp.log文件。

### 覆盖默认的累积行为
如果默认累积行为不适合您的需要，您可以通过将可加性标志设置为false来覆盖它。 因此，记录器树中的一个分支可能会将输出引导到一组不同于其余树的appender。
##### 可加性标志 (src/main/resources/chapters/configuration/additivityFlag.xm)
```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>foo.log</file>
    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file : %line] %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration.Foo" additivity="false">
    <appender-ref ref="FILE" />
  </logger>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
这个例子中，名为FILE的appender被附加到chapter.configuration.Foo记录器。 此外，章节.configuration.Foo记录器将其可加性标志设置为false，以便将其日志输出发送给名为FILE的appender，但不发送给层次结构中更高级的任何appender。 其他记录器仍然无视章节的配置.Foo记录器的可加性设置。 使用additivityFlag.xml配置文件运行MyApp3应用程序将从chapters.configuration.MyApp3记录器在控制台上输出结果。 但是，来自章节.configuration.Foo记录器的输出将显示在foo.log文件中，并且只出现在该文件中。

### 设置上下文名称
正如在前面的章节中提到的，每个记录器都附加到记录器上下文中。 默认情况下，记录器上下文称为“default”。 但是，您可以借助<contextName>配置指令设置不同的名称。 请注意，一旦设置，记录器上下文名称不能更改。 设置上下文名称是一种简单而直接的方法，用于区分登录到同一目标的多个应用程序。

##### 设置上下文名称并显示它(contextName.xml)
```
<configuration>
  <contextName>myAppName</contextName>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d %contextName [%t] %level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
### 变量替代
本文档的早期版本使用术语“属性替代”而不是术语“变量”。 请考虑这两个术语可以互换，尽管后一个术语表达更清楚的含义。

和许多脚本语言一样，logback配置文件支持变量的定义和替换。 变量有一个范围（见下文）。 此外，可以在配置文件本身，外部文件，外部资源中定义变量，甚至可以在运行中计算和定义变量。

变量替换可以发生在配置文件中可以指定值的任何位置。 变量替换的语法类似于Unix shell的语法。 开始$ {和关闭}之间的字符串被解释为对该属性值的引用。 对于属性aName，字符串“$ {aName}”将被替换为aName属性所持有的值。

由于它们经常派上用场，HOSTNAME和CONTEXT_NAME变量会自动定义并具有上下文范围。 鉴于在某些环境下计算主机名可能需要一些时间，其值会延迟计算（仅在需要时）。 而且，HOSTNAME可以直接在配置中设置。

### 定义变量
可以在配置文件本身中一次定义一个变量，或者从外部属性文件或外部资源批量加载变量。 由于历史原因，用于定义变量的XML元素是<property>，尽管在logback 1.0.7中，元素<variable>可以互换使用。

下一个示例显示在配置文件的开头声明的变量。 然后在文件中使用它来指定输出文件的位置。

##### variableSubstitution1.xml
```
<configuration>

  <property name="USER_HOME" value="/home/sebastien" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${USER_HOME}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```
下一个示例显示使用System属性来实现相同的结果。 该属性未在配置文件中声明，因此logback将在系统属性中查找它。 Java系统属性可以在命令行上设置，如下所示：

```
java -DUSER_HOME="/home/sebastien" MyApp2
```
```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${USER_HOME}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```
当需要多个变量时，创建一个包含所有变量的单独文件可能会更方便。 这是如何做这样的设置。

```
<configuration>

  <property file="src/main/java/chapters/configuration/variables1.properties" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
     <file>${USER_HOME}/myApp.log</file>
     <encoder>
       <pattern>%msg%n</pattern>
     </encoder>
   </appender>

   <root level="debug">
     <appender-ref ref="FILE" />
   </root>
</configuration>
```
该配置文件包含对名为variables1.properties的文件的引用。 包含在该文件中的变量将被读取，然后在本地范围内定义。 以下是variable.properties文件的外观。
##### 
```
<configuration>

  <property resource="resource1.properties" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
     <file>${USER_HOME}/myApp.log</file>
     <encoder>
       <pattern>%msg%n</pattern>
     </encoder>
   </appender>

   <root level="debug">
     <appender-ref ref="FILE" />
   </root>
</configuration>
```

### 领域
ScopesaA属性可以定义为插入到本地范围，上下文范围或系统范围中。 本地范围是默认值。 尽管可以从OS环境读取变量，但无法写入OS环境。

LOCAL SCOPE本地作用域的属性从配置文件中的定义处存在，直到解释/执行所述配置文件结束。 作为推论，每次配置文件被解析和执行时，局部范围内的变量都会重新定义。

上下文范围具有上下文范围的属性被插入到上下文中，并随上下文一直持续或直到它被清除。 一旦定义，上下文范围中的属性就是上下文的一部分。 因此，它在所有日志事件中都可用，包括通过序列化发送到远程主机的日志事件。

系统范围具有系统范围的属性被插入到JVM的系统属性中，并且只要JVM一直存在或直到被清除。

在替换过程中，首先在本地范围内查找属性，在上下文范围内查找属性，在系统属性范围的第三个范围内查找属性，在操作系统环境的第四个和最后一个范围查找属性

他可以使用<property>元素的scope属性，<define>元素或<insertFromJNDI>元素来设置属性的范围。 范围属性允许“本地”，“上下文”和“系统”字符串作为可能的值。 如果未指定，范围始终假定为“本地”。

##### contextScopedVariable.xml
```
<configuration>

  <property scope="context" name="nodeId" value="firstNode" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>/opt/${nodeId}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```
在上面的例子中，假设在上下文范围中定义了nodeId属性，它将在每个日志记录事件中都可用，即使是通过序列化发送到远程主机的事件。

### 变量的默认值
在某些情况下，如果变量没有声明或者其值为空，那么可能需要一个默认值。 和Bash shell一样，可以使用“： - ”运算符来指定默认值。 例如，假设未定义名为aName的变量，则“$ {aName：-golden}”将被解释为“golden”。

### 嵌套变量
变量嵌套完全受支持。 变量的名称，默认值和值定义都可以引用其他变量。

### 值嵌套
变量的值定义可以包含对其他变量的引用。 假设您希望使用变量来指定目标目录以及文件名，并将这两个变量合并到名为“destination”的第三个变量中。 下面显示的属性文件给出了一个例子。

```
USER_HOME=/home/sebastien
fileName=myApp.log
destination=${USER_HOME}/${fileName}
```
请注意，在上面的属性文件中，“destination”由两个其他变量组成，即“USER_HOME”和“fileName”。
##### 使用单独的文件进行变量替换
```
<configuration>

  <property file="variables2.properties" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${destination}</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
```

#### 名称嵌套
当引用一个变量时，变量名可能包含对另一个变量的引用。例如，如果名为“userid”的变量被分配了值“alice”，那么“$ {$ {userid} .password}”引用名称为“alice.password”的变量。

#### 默认值嵌套
变量的默认值可以引用另一个变量。例如，假设变量'id'未被赋值并且变量'userid'被赋值为“alice”，则表达式“$ {id： - $ {userid}}”将返回“alice”。

#### HOSTNAME属性
由于它经常派上用场，因此在使用上下文范围进行配置期间，会自动定义HOSTNAME属性。

#### CONTEXT_NAME属性
正如其名称所示，CONTEXT_NAME属性对应于当前日志记录上下文的名称。

```
<configuration>

  <define name="rootLevel" class="a.class.implementing.PropertyDefiner">
    <shape>round</shape>
    <color>brown</color>
    <size>24</size>
  </define>
 
  <root level="${rootLevel}"/>
</configuration>
```
在上面的例子中，形状，颜色和大小是“a.class.implementing.PropertyDefiner”的属性。 只要在您实现PropertyDefiner实例时存在给定属性的setter，logback就会根据配置文件中的指定注入适当的值。

### 有条件处理配置文件
开发人员通常需要在针对不同环境（如开发，测试和生产）的多个logback配置文件之间进行调整。 这些配置文件在很多地方都有很多不同的地方。 为了避免重复，logback支持在<if>，<then>和<else>元素的帮助下对配置文件进行条件处理，以便单个配置文件可以充分地针对多个环境。 请注意，条件处理需要Janino库。

```
<!-- if-then form -->
   <if condition="some conditional expression">
    <then>
      ...
    </then>
  </if>
  
  <!-- if-then-else form -->
  <if condition="some conditional expression">
    <then>
      ...
    </then>
    <else>
      ...
    </else>    
  </if>
```
该条件是一个只能访问上下文属性或系统属性的Java表达式。 对于作为参数传递的键，property（）或其较短的等效p（）方法返回属性的String值。 例如，要使用键“k”访问属性的值，可以写属性（“k”）或等价的p（“k”）。 如果具有键“k”的属性未定义，那么属性方法将返回空字符串，而不是空值。 这避免了需要检查空值。

isDefined（）方法可用于检查属性是否已定义。 例如，要检查是否定义了属性“k”，可以编写isDefined（“k”）类似地，如果您需要检查某个属性是否为null，则会提供isNull（）方法。 例如：isNull（“k”）。

```
<configuration debug="true">

  <if condition='property("HOSTNAME").contains("torino")'>
    <then>
      <appender name="CON" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
          <pattern>%d %-5level %logger{35} - %msg %n</pattern>
        </encoder>
      </appender>
      <root>
        <appender-ref ref="CON" />
      </root>
    </then>
  </if>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${randomOutputDir}/conditional.log</file>
    <encoder>
      <pattern>%d %-5level %logger{35} - %msg %n</pattern>
   </encoder>
  </appender>

  <root level="ERROR">
     <appender-ref ref="FILE" />
  </root>
</configuration>
```
条件处理在<configuration>元素中的任何地方都受支持。 嵌套的if-then-else语句也被支持。 但是，XML语法非常麻烦，不适合作为通用编程语言的基础。 因此，太多的条件会很快使您的配置文件无法理解，包括您自己在内的后续读者。

### 从JNDI获取变量
在某些情况下，您可能想要使用存储在JNDI中的env-entries。 <insertFromJNDI>配置指令提取存储在JNDI中的env条目，并使用由as属性指定的键将该属性插入到本地作用域中。 作为所有属性，可以借助scope属性将新属性插入到不同的作用域中。

##### 作为通过JNDI获取的属性env-entries插入
```
<configuration>
  <insertFromJNDI env-entry-name="java:comp/env/appName" as="appName" />
  <contextName>${appName}</contextName>

  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d ${CONTEXT_NAME} %level %msg %logger{50}%n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="CONSOLE" />
  </root>
</configuration>
```
在这最后一个例子中，“java：comp / env / appName”env-entry被作为appName属性插入。 请注意，<contextName>指令根据前一个<insertFromJNDI>指令插入的appName属性的值设置上下文名称。

### 文件包含
Joran支持从其他文件中包含配置文件的一部分。 这是通过声明一个<include>元素完成的，如下所示：

```
<configuration>
  <include file="src/main/java/chapters/configuration/includedConfig.xml"/>

  <root level="DEBUG">
    <appender-ref ref="includedConsole" />
  </root>

</configuration>
```
目标文件必须将其元素嵌套在<included>元素中。 例如，一个ConsoleAppender可以声明为：

```
<included>
  <appender name="includedConsole" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>"%d - %m%n"</pattern>
    </encoder>
  </appender>
</included>

```

请再次注意强制的<included>元素。

要包含的内容可以作为文件，资源或URL引用。

作为一个文件：
要包含文件，请使用文件属性。 您可以使用相对路径，但请注意，当前目录由应用程序定义，并且不一定与配置文件的路径相关。
作为资源：
要包含资源，即在类路径中找到的文件，请使用资源属性。
```
<include resource =“includedConfig.xml”/>
```
作为网址：
要包含URL的内容，请使用url属性。
```
<include url =“http://some.host.com/includedConfig.xml”/>
```
如果找不到要包含的文件，logback将通过打印状态消息来投诉。 如果包含的文件是可选的，则可以通过在<include>元素中将可选属性设置为true来禁止警告消息。
```
<include optional =“true”.... />
```
### 添加一个上下文监听器
#### LevelChangePropagator
从版本0.9.25开始，logback-classic附带了LevelChangePropagator，它是LoggerContextListener的一个实现，它将对任何logback-classic记录器级别所做的更改传播到java.util.logging框架。 这种传播消除了禁用的日志语句对性能的影响。 LogRecord的实例将仅发送到logback（通过SLF4J）用于启用日志语句。 这使得真实世界的应用程序使用jul-to-slf4j桥是合理的。

contextListener元素可用于安装LevelChangePropagator，如下所示。

```
<configuration debug="true">
  <contextListener class="ch.qos.logback.classic.jul.LevelChangePropagator"/>
  .... 
</configuration>
```
设置LevelChangePropagator的resetJUL属性将重置所有j.u.l的所有以前的级别配置。记录仪。 但是，以前安装的处理程序将保持不变。

```
<configuration debug="true">
  <contextListener class="ch.qos.logback.classic.jul.LevelChangePropagator">
    <resetJUL>true</resetJUL>
  </contextListener>
  ....
</configuration>
```