# Appender
## 什么是Appender
Logback将写入日志事件的任务委托给名为appender的组件。 Appender必须实现ch.qos.logback.core.Appender接口。 这个接口的显着方法总结如下：
```
package ch.qos.logback.core;
  
import ch.qos.logback.core.spi.ContextAware;
import ch.qos.logback.core.spi.FilterAttachable;
import ch.qos.logback.core.spi.LifeCycle;
  

public interface Appender<E> extends LifeCycle, ContextAware, FilterAttachable {

  public String getName();
  public void setName(String name);
  void doAppend(E event);
  
}
```
Appender接口中的大多数方法都是setter和getters。一个值得注意的例外是doAppend（）方法将E类型的对象实例作为其唯一参数。 E的实际类型将根据logback模块而有所不同。在logback-classic模块中，E将是ILoggingEvent类型，而在logback-access模块​​中，它将是AccessEvent类型。 doAppend（）方法可能是logback框架中最重要的方法。它负责将适当格式的记录事件输出到适当的输出设备。

Appender是命名实体。这确保了它们可以通过名称被引用，这是一种被确认在配置脚本中很有用的质量。 Appender接口扩展了FilterAttachable接口。因此可以将一个或多个过滤器附加到appender实例。过滤器将在后面的章节中详细讨论。

Appenders最终负责输出日志记录事件。但是，他们可以将事件的实际格式委托给布局或编码器对象。每个布局/编码器都与一个且只有一个appender关联，称为拥有appender。一些appender具有内置或固定的事件格式。因此，他们不需要也没有布局/编码器。例如，SocketAppender只需将日志事件序列化，然后再通过线路传输它们。


## AppenderBase
ch.qos.logback.core.AppenderBase类是实现Appender接口的抽象类。 它提供了所有appender共享的基本功能，例如获取或设置其名称的方法，激活状态，布局和过滤器。 它是与logback一起发运的所有appender的超级类别。 尽管是一个抽象类，AppenderBase实际上在Append接口中实现了doAppend（）方法。 也许讨论AppenderBase类最清晰的方法是通过提供实际源代码的摘录。

```
public synchronized void doAppend(E eventObject) {

  // prevent re-entry.
  if (guard) {
    return;
  }

  try {
    guard = true;

    if (!this.started) {
      if (statusRepeatCount++ < ALLOWED_REPEATS) {
        addStatus(new WarnStatus(
            "Attempted to append to non started appender [" + name + "].",this));
      }
      return;
    }

    if (getFilterChainDecision(eventObject) == FilterReply.DENY) {
      return;
    }
    
    // ok, we now invoke the derived class's implementation of append
    this.append(eventObject);

  } finally {
    guard = false;
  }
}
```
doAppend（）方法的这个实现是同步的。由此可见，从不同线程登录到相同的appender是安全的。当一个线程（比如说T）正在执行doAppend（）方法时，其他线程的后续调用将排队，直到T离开doAppend（）方法，从而确保T独占访问appender。

由于这种同步并不总是合适的，logback附带了与AppenderBase类非常相似的ch.qos.logback.core.UnsynchronizedAppenderBase。为了简洁起见，我们将在本文的其余部分讨论UnsynchronizedAppenderBase。

doAppend（）方法的第一件事是检查guard是否设置为true。如果是，它立即退出。如果守卫未设置，则在下一个语句中将其设置为true。警卫确保doAppend（）方法不会递归调用它自己。试想一下，在append（）方法之外调用的某个组件想要记录某些内容。它的调用可以被引导到刚刚调用它的同一个appender，导致无限循环和堆栈溢出。

在下面的语句中，我们检查起始字段是否为真。如果不是，doAppend（）将发送一条警告消息并返回。换句话说，一旦appender关闭，就不可能写入。 Appender对象实现了LifeCycle接口，这意味着它们实现了start（），stop（）和isStarted（）方法。在设置appender的所有属性后，logback的配置框架Joran会调用start（）方法来通知appender激活其属性。根据其种类的不同，如果某些属性丢失或者由于各种属性之间的干扰，appender可能无法启动。例如，假定文件创建取决于截断模式，则FileAppender不能对其“文件”选项的值进行操作，直到Append选项的值也可以确定。显式激活步骤可确保appender在其值已知之后对属性起作用。

如果appender无法启动或者已停止，则会通过logback的内部状态管理系统发出警告消息。经过多次尝试后，为避免内部状态系统泛滥同样的警告消息，doAppend（）方法将停止发出这些警告。

下一个if语句检查附加过滤器的结果。根据过滤器链的决定，可以拒绝或明确接受事件。在没有过滤器链决定的情况下，默认情况下会接受事件。

doAppend（）方法然后调用派生类的append（）方法的实现。该方法执行将事件附加到适当设备的实际工作。

最后，释放警卫以允许随后调用doAppend（）方法。

对于本手册的其余部分，我们保留术语“选项”或“属性”，用于通过setter和getter方法使用JavaBeans内省动态推断的任何属性。

## Logback-core
Logback-core奠定了构建其他Logback模块的基础。 一般来说，logback-core中的组件需要一些（尽管很少）定制。 但是，在接下来的几节中，我们将介绍几种可以立即使用的appender。

### OutputStreamAppender
OutputStreamAppender将事件追加到java.io.OutputStream。 此课程提供其他appender基于的基本服务。 用户通常不直接实例化OutputStreamAppender对象，因为通常java.io.OutputStream类型不能方便地映射到字符串，因为无法在配置脚本中指定目标OutputStream对象。 简而言之，您无法从配置文件中配置OutputStreamAppender。 但是，这并不意味着OutputStreamAppender缺少可配置的属性。 接下来介绍这些属性。

属性名称 | type | desc 
--- | --- | ---
encoder | [Encoder](https://logback.qos.ch/xref/ch/qos/logback/core/encoder/Encoder.html) | 确定事件写入底层OutputStreamAppender的方式。 编码器在专用章节中介绍。
immediateFlush | boolean | immediateFlush的默认值是'true'。 即时刷新输出流可确保记录事件立即写出并且在您的应用程序退出但未正确关闭appender时不会丢失。 另一方面，将此属性设置为'false'可能会使记录吞吐量翻两番（您的里程可能会有所不同）。 同样，如果immediateFlush设置为'false'，并且如果appender在应用程序退出时未正确关闭，那么记录尚未写入磁盘的事件可能会丢失。

OutputStreamAppender是另外三个appender的超类，即ConsoleAppender，FileAppender，它又是RollingFileAppender的超类。 下图说明了OutputStreamAppender及其子类的类图。

![appenderClassDiagram.jpg](./appenderClassDiagram.jpg)

### ConsoleAppender
ConsoleAppender（正如名称所示）附加在控制台上，或者更确切地说在System.out或System.err上，前者是默认目标。 ConsoleAppender在用户指定的编码器的帮助下对事件进行格式化。 编码器将在随后的章节中讨论。 System.out和System.err都是java.io.PrintStream类型。 因此，它们被封装在缓冲I / O操作的OutputStreamWriter中。

Property Name	|Type|	Description
---|---|---
encoder | Encoder | 请参阅OutputStreamAppender属性
target | String | 其中一个字符串值为System.out或System.err。 默认目标是System.out
withJansi | boolean | 默认情况下，使用Jansi属性设置为false。 使用Jansi设置为true将激活Jansi库，该库为Windows机器上的ANSI颜色代码提供支持。 在Windows主机上，如果此属性设置为true，则应该在类路径中放置“org.fusesource.jansi：jansi：1.9”。 请注意，基于Unix的操作系统（如Linux和Mac OS X）默认支持ANSI颜色

##### logback-Console.xml
```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
### FileAppender
FileAppender是OutputStreamAppender的子类，它将日志事件附加到文件中。 目标文件由File选项指定。 如果该文件已存在，则根据append属性的值将其附加到或截断。

Property Name|	Type|	Description
--- | --- | ---
append | boolean |  如果为true，则将事件附加到现有文件的末尾。 否则，如果append为false，则任何现有文件都将被截断。 追加选项默认设置为true。
encoder | Encoder | 请参阅OutputStreamAppender属性
file | String | 要写入的文件的名称。 如果该文件不存在，则会创建该文件。 在MS Windows平台上，用户经常忘记避开反斜杠
prudent | boolean |在谨慎的模式下，FileAppender将安全地写入指定的文件，即使存在运行在不同JVM中的其他FileAppender实例，也可能运行在不同的主机上。 谨慎模式的默认值为false。

谨慎模式可以与RollingFileAppender结合使用，尽管有一些限制。

谨慎模式意味着append属性自动设置为true。

谨慎更多地依赖于独占文件锁定。实验表明，文件锁定大约是写入日志记录事件成本的三倍（x3）。在“平均”PC上写入位于本地硬盘上的文件时，谨慎模式关闭时，写入单个日志记录事件大约需要10微秒。启动谨慎模式时，输出单个记录事件大约需要30微秒。这意味着当审慎模式关闭时，每秒记录吞吐量为100'000个事件，在审慎模式下每秒记录约33'000个事件。

谨慎模式有效地序列化写入同一文件的所有JVM之间的I / O操作。因此，随着竞争访问文件的JVM数量的增加，每个I / O操作所引起的延迟也会增加。只要I / O操作的总数量为每秒20个日志请求的数量级，对性能的影响应该可以忽略不计。每秒产生100个或更多I / O操作的应用程序可能会对性能产生影响，应避免使用审慎模式。

联网文件锁当日志文件位于联网文件系统上时，审慎模式的成本更高。同样重要的是，对网络文件系统的文件锁定有时会产生强烈的偏见，以致当前拥有该锁的进程在其释放后立即重新获得该锁。因此，当一个进程占用日志文件的锁定时，其他进程就会等待锁定到出现死锁的地步。

审慎模式的影响主要取决于网络速度以及操作系统实施细节。我们提供了一个称为FileLockSimulator的非常小的应用程序，它可以帮助您模拟环境中谨慎模式的行为。

默认情况下，每个日志事件立即刷新到基础输出流。 这种默认方法更安全，因为如果您的应用程序没有正确关闭appender而退出，日志事件不会丢失。 但是，为了显着提高日志记录吞吐量，您可能需要将immediateFlush属性设置为false。

##### logback-fileAppender.xml
```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>testFile.log</file>
    <append>true</append>
    <!-- set immediateFlush to false for much higher logging throughput -->
    <immediateFlush>true</immediateFlush>
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
    </encoder>
  </appender>
        
  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

### 唯一命名的文件（按时间戳）
在应用程序开发阶段或在短期应用程序的情况下，例如 批处理应用程序时，最好在每次启动新应用程序时创建一个新的日志文件。 这很容易在<timestamp>元素的帮助下完成。 这是一个例子。
##### logback-timestamp.xml
```
<configuration>

  <!-- Insert the current time formatted as "yyyyMMdd'T'HHmmss" under
       the key "bySecond" into the logger context. This value will be
       available to all subsequent configuration elements. -->
  <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <!-- use the previously created timestamp to create a uniquely
         named log file -->
    <file>log-${bySecond}.txt</file>
    <encoder>
      <pattern>%logger{35} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```
timestamp元素采用两个强制属性key和datePattern以及一个可选的timeReference属性。 关键属性是时间戳将作为变量可供后续配置元素使用的密钥的名称。 datePattern属性表示用于将当前时间（配置文件被解析的时间）转换为字符串的日期模式。 日期模式应该遵循SimpleDateFormat中定义的约定。 timeReference属性表示时间戳的时间基准。 缺省值是配置文件的解释/解析时间，即当前时间。 但是，在某些情况下，将上下文出生时间用作时间参考可能很有用。 这可以通过将timeReference属性设置为“contextBirth”来实现。

要使用记录器上下文出生日期作为时间参考，可以将timeReference属性设置为“contextBirth”，如下所示。

##### logback-timestamp-contextBirth.xml
```
<configuration>
  <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss" 
             timeReference="contextBirth"/>
  ...
</configuration>
```

### RollingFileAppender
RollingFileAppender扩展了FileAppender，具有翻转日志文件的功能。 例如，RollingFileAppender可以登录到名为log.txt文件的文件，并且一旦满足某个条件，就将其日志目标更改为另一个文件。

有两个与RollingFileAppender交互的重要子组件。 第一个RollingFileAppender子组件，即RollingPolicy（见下文）负责执行滚动所需的操作。 RollingFileAppender的第二个子组件，即TriggeringPolicy（见下文）将确定是否以及确切地发生翻转时。 因此，RollingPolicy负责什么和TriggeringPolicy是什么时候负责的。

为了任何用途，RollingFileAppender必须同时设置RollingPolicy和TriggeringPolicy。 但是，如果其RollingPolicy也实现了TriggeringPolicy接口，则只需要明确指定前者。

Property Name|	Type	|Description
--- | --- | ---
file | String | 请参阅FileAppender属性。
append | boolean | 
encoder | Encoder |
rollingPolicy | RollingPolicy | 此选项是组件，它将决定发生翻滚时RollingFileAppender的行为。 请参阅以下更多信息。
triggeringPolicy | TriggeringPolicy	 | 此选项是将告诉RollingFileAppender何时激活翻转过程的组件。 请参阅以下更多信息。
rudent |  boolean | FixedWindowRollingPolicy在审慎模式下不受支持

### 滚动策略概述
RollingPolicy负责涉及文件移动和重命名的翻转过程。

RollingPolicy界面如下所示：
```
package ch.qos.logback.core.rolling;  

import ch.qos.logback.core.FileAppender;
import ch.qos.logback.core.spi.LifeCycle;

public interface RollingPolicy extends LifeCycle {

  public void rollover() throws RolloverFailure;
  public String getActiveFileName();
  public CompressionMode getCompressionMode();
  public void setParent(FileAppender appender);
}
```
翻转方法完成了归档当前日志文件所涉及的工作。 调用getActiveFileName（）方法来计算当前日志文件的文件名（其中写入实时日志）。 如getCompressionMode方法所示，RollingPolicy还负责确定压缩模式。 最后，通过setParent方法给RollingPolicy一个对其父代的引用。

##### TimeBasedRollingPolicy
TimeBasedRollingPolicy可能是最受欢迎的滚动策略。 它定义了基于时间的翻转策略，例如按天或按月。 TimeBasedRollingPolicy承担翻转和触发翻滚的责任。 事实上，TimeBasedTriggeringPolicy实现了RollingPolicy和TriggeringPolicy接口。

TimeBasedRollingPolicy的配置需要一个必需的fileNamePattern属性和几个可选属性。

Property Name|	Type|	Description
--- | --- | ---
fileNamePattern | String |
maxHistory | int |
totalSizeCap | int |
cleanHistoryOnStart | boolean | 

