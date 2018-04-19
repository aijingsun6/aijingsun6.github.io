# [layouts](https://logback.qos.ch/manual/layouts.html)

## 什么是布局？
如果你想知道，布局与佛罗里达州的大型庄园无关。 布局是logback组件，负责将传入事件转换为String。 Layout接口中的format（）方法接收一个表示（任何类型）事件的对象并返回一个String。 下面显示了Layout界面的大纲。
```
public interface Layout<E> extends ContextAware, LifeCycle {

  String doLayout(E event);
  String getFileHeader();
  String getPresentationHeader();
  String getFileFooter();
  String getPresentationFooter();
  String getContentType();
}
```
这个界面相当简单，但对于许多格式化需求已足够。 来自得克萨斯州的德克萨斯开发商，你可能从约瑟夫海勒的Catch-22中了解到，他可能会说：只需要五种方法来实现布局！


## Logback-classic

Logback-classic连接到仅处理类型为ch.qos.logback.classic.spi.ILoggingEvent的事件。 这一事实在本节中将很明显。

### 编写自己的自定义布局
让我们为logback-classic模块实现一个简单但功能齐全的布局，它打印自应用程序启动以来的时间，记录事件的级别，括号内的调用者线程，记录器名称，短划线以及事件消息 和一条新线。

示例输出可能如下所示：

```
10489 DEBUG [main] com.marsupial.Pouch - Hello world.
```
```
package chapters.layouts;

import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.core.LayoutBase;

public class MySampleLayout extends LayoutBase<ILoggingEvent> {

  public String doLayout(ILoggingEvent event) {
    StringBuffer sbuf = new StringBuffer(128);
    sbuf.append(event.getTimeStamp() - event.getLoggingContextVO.getBirthTime());
    sbuf.append(" ");
    sbuf.append(event.getLevel());
    sbuf.append(" [");
    sbuf.append(event.getThreadName());
    sbuf.append("] ");
    sbuf.append(event.getLoggerName();
    sbuf.append(" - ");
    sbuf.append(event.getFormattedMessage());
    sbuf.append(CoreConstants.LINE_SEP);
    return sbuf.toString();
  }
}
```
请注意，MySampleLayout扩展了LayoutBase。此类管理所有布局实例通用的状态，例如布局是开始还是停止，页眉，页脚和内容类型数据。它允许开发人员专注于从他/她的布局中预期的格式。请注意，LayoutBase类是通用的。在它的类声明中，MySampleLayout扩展了LayoutBase <ILoggingEvent>。

doLayout（ILoggingEvent事件）方法，即MySampleLayout中唯一的方法，首先实例化一个StringBuffer。它通过添加事件参数的各个字段来继续。德克萨斯州的德克萨斯人小心地打印格式化的信息。如果一个或多个参数与日志记录请求一起传递，这是非常重要的。

在将这些各种字符添加到字符串缓冲区后，doLayout（）方法将缓冲区转换为字符串并返回结果值。

在上面的例子中，doLayout方法忽略了事件中包含的任何最终异常。在真实世界的布局实现中，您最有可能还想打印异常的内容。

## 配置您的自定义布局
自定义布局配置为任何其他组件。 如前所述，FileAppender及其子类需要一个编码器。 为了满足这个要求，我们传递给FileAppender一个包装我们的MySampleLayout的LayoutWrappingEncoder的实例。 这是配置文件：
##### sampleLayoutConfig.xml
```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
      <layout class="chapters.layouts.MySampleLayout" />
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
示例应用程序章节.layouts.SampleLogging将配置脚本配置为使用作为其第一个参数传递的配置脚本，然后记录一条调试消息，随后出现错误消息。

要运行此示例，请在logback-examples目录中发出以下命令。

```
java chapters.layouts.SampleLogging src/main/java/chapters/layouts/sampleLayoutConfig.xml
0 DEBUG [main] chapters.layouts.SampleLogging - Everything's going well
0 ERROR [main] chapters.layouts.SampleLogging - maybe not quite...

```
这很简单。 Elea的怀疑者Pyrrho坚持认为没有任何东西是确定的，除了可能的不确定性本身，这也决不是肯定的，可能会问：如何选择布局？ 读者将在MySampleLayout2.java中找到我们自定义布局的稍微修改版本。 正如在本手册中提到的那样，向布局或任何其他logback组件添加属性与为该属性声明setter方法一样简单。

MySampleLayout2类包含两个属性。 第一个是可以添加到输出的前缀。 第二个属性用于选择是否显示发送日志记录请求的线程的名称。

```
package chapters.layouts;

import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.core.LayoutBase;

public class MySampleLayout2 extends LayoutBase<ILoggingEvent> {

  String prefix = null;
  boolean printThreadName = true;

  public void setPrefix(String prefix) {
    this.prefix = prefix;
  }

  public void setPrintThreadName(boolean printThreadName) {
    this.printThreadName = printThreadName;
  }

  public String doLayout(ILoggingEvent event) {
    StringBuffer sbuf = new StringBuffer(128);
    if (prefix != null) {
      sbuf.append(prefix + ": ");
    }
    sbuf.append(event.getTimeStamp() - event.getLoggerContextVO().getBirthTime());
    sbuf.append(" ");
    sbuf.append(event.getLevel());
    if (printThreadName) {
      sbuf.append(" [");
      sbuf.append(event.getThreadName());
      sbuf.append("] ");
    } else {
      sbuf.append(" ");
    }
    sbuf.append(event.getLoggerName());
    sbuf.append(" - ");
    sbuf.append(event.getFormattedMessage());
    sbuf.append(LINE_SEP);
    return sbuf.toString();
  }
}
```
只需添加相应的setter方法即可启用属性配置。 请注意，PrintThreadName属性是布尔值而不是字符串。 关于配置的章节详细介绍了logback组件的配置。 关于Joran的章节提供了更多细节。 这里是为MySampleLayout2量身定做的配置文件。

```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
      <layout class="chapters.layouts.MySampleLayout2"> 
        <prefix>MyPrefix</prefix>
        <printThreadName>false</printThreadName>
      </layout>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
## PatternLayout
Logback经典船舶具有名为PatternLayout的灵活布局。作为所有的布局，PatternLayout接受一个日志事件并返回一个String。然而，这个String可以通过调整PatternLayout的转换模式来定制。

PatternLayout的转换模式与C编程语言中printf（）函数的转换模式密切相关。转换模式由称为转换说明符的文本文本和格式控制表达式组成。您可以自由地在转换模式中插入任何文本文本。每个转换说明符以百分号“％”开头，后跟可选的格式修饰符，转换词和大括号之间的可选参数。转换字控制数据字段以例如转换记录器名称，级别，日期或线程名称。格式修饰符控制字段宽度，填充以及左对齐或右对齐。

正如前面已经提到的那样，FileAppender和子类需要一个编码器。因此，当与FileAppender或其子类一起使用时，PatternLayout必须包装在编码器中。鉴于FileAppender / PatternLayout组合非常常见，logback附带一个名为PatternLayoutEncoder的编码器，该编码器仅用于封装PatternLayout实例，以便将其视为编码器。下面是一个使用PatternLayoutEncoder以编程方式配置ConsoleAppender的示例：

```
package chapters.layouts;

import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.Logger;
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.encoder.PatternLayoutEncoder;
import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.core.ConsoleAppender;

public class PatternSample {

  static public void main(String[] args) throws Exception {
    Logger rootLogger = (Logger)LoggerFactory.getLogger(Logger.ROOT_LOGGER_NAME);
    LoggerContext loggerContext = rootLogger.getLoggerContext();
    // we are not interested in auto-configuration
    loggerContext.reset();

    PatternLayoutEncoder encoder = new PatternLayoutEncoder();
    encoder.setContext(loggerContext);
    encoder.setPattern("%-5level [%thread]: %message%n");
    encoder.start();

    ConsoleAppender<ILoggingEvent> appender = new ConsoleAppender<ILoggingEvent>();
    appender.setContext(loggerContext);
    appender.setEncoder(encoder); 
    appender.start();

    rootLogger.addAppender(appender);

    rootLogger.debug("Message 1"); 
    rootLogger.warn("Message 2");
  } 
}
```

在上例中，转换模式设置为“％-5级别[％thread]：％message％n”。 包含在logback中的转换词的概要将很快给出。 将PatternSample应用程序运行为：

请注意，在转换模式“％-5level [％thread]：％message％n”中，文本文本和转换说明符之间没有明确的分隔符。解析转换模式时，PatternLayout能够区分文本文本（空格字符，括号，冒号字符）和转换说明符。在上面的例子中，转换说明符％-5level表示日志记录事件的级别应该左对齐为五个字符的宽度。格式说明符将在下面解释。

在PatternLayout中，可以使用括号来对转换模式进行分组。因此，'（'和'）'具有特殊含义，如果打算用作文字，则需要转义。括号的特殊性在下面进一步解释。

如前所述，某些转换说明符可能包括大括号之间传递的可选参数。带选项的示例转换说明符可以是％logger {10}。这里“记录器”是转换字，10是选项。选项将在下面进一步讨论。

下表描述了公认的转换词及其选项。当多个转换单词在同一个表单元格中列出时，它们被视为别名。
| Conversion Word | Effect |
| --- | --- |
| ```c{length}/lo{length}/logger{length}```| |
|```C{length}/class{length}``` | |
| ```contextName/cn``` | |
| ```d{pattern}/date{pattern}``` | |
|```F / file``` |
|```caller{depth}/caller{depthStart..depthEnd}``` |
|```L / line``` |
|```m / msg / message```|
|```M/method```|
|```n```|
|```p / le / level```|
|```r / relative```|
|```t / thread```|
|```ex{depth}/exception{depth}/throwable{depth}```|```%ex/%ex{short}/%ex{full}/%ex{2}```|
...
鉴于在转换模式的情况下，百分号具有特殊含义，为了将其包括为文字，它需要用反斜线（例如， “％d％p \％％m％n”。

##### 转换单词后立即限制文字
在大多数情况下，文字自然包含空格或其他分隔字符，以免与转换词混淆。 例如，模式“％level [％thread] - ％message％n”包含字符串文字“[”和“] - ”。 然而，如果一个可以成为java标识符一部分的字符紧跟在一个转换词后面，logback的模式解析器会被愚弄，认为该文字是转换词的一部分。 例如，模式“％date％nHello”将被解释为两个转换字％date和％nHello，并且由于％nHello不是已知的转换字，因此logback将为％nHello输出％PARSER_ERROR [nHello]。 如果希望字符串文字“Hello”立即将％n和Hello分开，请将空参数列表传递给％n。 例如，“％date％n {} Hello”将被解释为％日期，然后是％n，后跟字面“Hello”。

#### 格式修饰符
默认情况下，相关信息按原样输出。但是，借助格式修饰符，可以更改每个数据字段的最小和最大宽度以及理由。

可选的格式修饰符位于百分号和转换字符或单词之间。

第一个可选的格式修饰符是左对齐标志，它只是减号（ - ）字符。然后是可选的最小字段宽度修改器。这是一个十进制常量，表示要输出的最少字符数。如果数据项包含较少的字符，则将其填充到左侧或右侧，直到达到最小宽度。默认是填充左边（右对齐），但您可以使用左对齐标志指定正确的填充。填充字符是空格。如果数据项大于最小字段宽度，则字段将扩展以适应数据。该值永远不会被截断。

此行为可以使用最大字段宽度修饰符进行更改，该修饰符由一个句点和一个小数常数指定。如果数据项长于最大字段，则会从数据项的开头删除多余的字符。例如，如果最大字段宽度为8，数据项的长度为10个字符，则数据项的前两个字符将被丢弃。这种行为偏离了C语言中的printf函数，从最后开始截断。

可以通过在该时间段之后追加减号来从结尾截断。在这种情况下，如果最大字段宽度为8，数据项的长度为10个字符，则数据项的最后两个字符将被丢弃。

以下是记录器转换说明符的各种格式修饰符示例。
Format modifier	|Left justify	|Minimum width	|Maximum width	|Comment
--- | --- | --- | --- | ---
%20logger |	false|	20	|none |如果记录器名称长度小于20个字符，则留有空格
%-20logger|	true|	20	|none| 如果记录器名称长度小于20个字符，请在空白处填写空格
%.30logger |NA|none|30|如果记录器名称长于30个字符，则从头开始截断。
%20.30logger|false|20|30| 如果记录器名称短于20个字符，则留有空格。 但是，如果记录器名称超过30个字符，则从left开始截断。
%-20.30logger|	true|	20	|30|如果记录器名称少于20个字符，请在空白处填写空格。 但是，如果记录器名称超过30个字符，则从right开始截断。
%.-30logger|	NA|	none|	30|如果记录器名称长于30个字符，则从截断结束。

下表列出了格式修饰符截断的示例。 请注意，方括号，即一对“[]”字符，不是输出的一部分。 它们用于分隔输出的宽度。

Format modifier	|Logger name|	Result
--- | --- | ---
[%20.20logger]	|main.Name	|```[           main.Name]```
[%-20.20logger]	|main.Name	|```[main.Name           ]```
[%10.10logger]	 |main.foo.foo.bar.Name	|```[o.bar.Name]```
[%10.-10logger]	 |main.foo.foo.bar.Name	|```[main.foo.f]```

输出关卡的一个字母

您可能只需要打印T，D，W，I和E，而不是打印TRACE，DEBUG，WARN，INFO或ERROR。您可以为此编写自定义转换器，或者仅使用格式修饰符 刚刚讨论过）将等级值缩短为单个字符。 适当的转换说明符将是“％.- 1level”。


### 转换字选项
转换说明符后面可以有选项。 总是在大括号之间声明。 我们已经看到了选项提供的一些可能性，例如结合MDC转换说明符，如：％mdc {someKey}。

转换说明符可能有多个选项。 例如，使用评估程序的转换说明符（即将覆盖）可以将评估程序名称添加到选项列表中，如下所示：

```
<pattern>%-4relative [%thread] %-5level - %msg%n %caller{2, DISP_CALLER_EVAL, OTHER_EVAL_NAME, THIRD_EVAL_NAME}</pattern>
```
如果选项包含特殊字符（如大括号，空格或逗号），则可以将其放在单引号或双引号之间。 例如，考虑下一个模式。
```
<pattern>%-5level - %replace(%msg){'\d{14,16}', 'XXXX'}%n</pattern>
```
我们将选项\ d {16}和XXXX传递给替换转换字。 它用XXXX替换消息中包含的任何14,15或16位数字，有效地混淆了信用卡号码。 请注意，“\ d”是正则表达式中单个数字的简写。 “{14,16 \}”被解释为“{14,16}”，即重复前一项至少14次，但最多16次。

### 括号是特殊的

在logback中，模式字符串中的括号被视为分组标记。 因此，可以对子模式进行分组并对该子模式应用格式化指令。 从版本0.9.27开始，logback支持复合转换单词，例如可以转换子模式的％替换。

```
%-30(%d{HH:mm:ss.SSS} [%thread]) %-5level %logger{32} - %msg%n
```
将对子模式“％d {HH：mm：ss.SSS} [％thread]”生成的输出进行分组，以便在少于30个字符时对其进行正确填充。

```
13:09:30 [main] DEBUG c.q.logback.demo.ContextListener - Classload hashcode is 13995234
13:09:30 [main] DEBUG c.q.logback.demo.ContextListener - Initializing for ServletContext
13:09:30 [main] DEBUG c.q.logback.demo.ContextListener - Trying platform Mbean server
13:09:30 [pool-1-thread-1] INFO  ch.qos.logback.demo.LoggingTask - Howdydy-diddly-ho - 0
13:09:38 [btpool0-7] INFO c.q.l.demo.lottery.LotteryAction - Number: 50 was tried.
13:09:40 [btpool0-7] INFO c.q.l.d.prime.NumberCruncherImpl - Beginning to factor.
13:09:40 [btpool0-7] DEBUG c.q.l.d.prime.NumberCruncherImpl - Trying 2 as a factor.
13:09:40 [btpool0-7] INFO c.q.l.d.prime.NumberCruncherImpl - Found factor 2
    
```

与“％-30（）”分组

```
13:09:30 [main]            DEBUG c.q.logback.demo.ContextListener - Classload hashcode is 13995234
13:09:30 [main]            DEBUG c.q.logback.demo.ContextListener - Initializing for ServletContext
13:09:30 [main]            DEBUG c.q.logback.demo.ContextListener - Trying platform Mbean server
13:09:30 [pool-1-thread-1] INFO  ch.qos.logback.demo.LoggingTask - Howdydy-diddly-ho - 0
13:09:38 [btpool0-7]       INFO  c.q.l.demo.lottery.LotteryAction - Number: 50 was tried.
13:09:40 [btpool0-7]       INFO  c.q.l.d.prime.NumberCruncherImpl - Beginning to factor.
13:09:40 [btpool0-7]       DEBUG c.q.l.d.prime.NumberCruncherImpl - Trying 2 as a factor.
13:09:40 [btpool0-7]       INFO  c.q.l.d.prime.NumberCruncherImpl - Found factor 2
    
```
后一种形式阅读起来更舒适。

如果您需要将括号字符视为文字，则需要通过在每个括号之前加上反斜杠进行转义。 如在\（％d {HH：mm：ss.SSS} [％thread] \）。

### 颜色
如上所述通过圆括号分组允许子模式的着色。 从版本1.0.5开始，PatternLayout识别“％black”，“％red”，“％green”，“％yellow”，“％blue”，“％magenta”，“％cyan”，“％white” ％bold“，”％boldred“，”％boldGreen“，”％boldYellow“，”％boldBlue“，”％boldMagenta“，％boldCyan，％boldWhite和％highlight作为转换词。 这些转换字旨在包含子模式。 任何由着色词包围的子模式都将以指定的颜色输出。

以下是一个说明着色的配置文件。 请注意包含“％logger {15}”的％cyan转换说明符。 这将输出缩写为青色的15个字符的记录器名称。 ％突出显示转换说明符以级别ERROR的事件以粗体显示其子模式，以WARN显示红色，以INFO显示BLUE以及显示其他级别的默认颜色。

##### highlighted.xml
```
<configuration debug="true">
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- On Windows machines setting withJansi to true enables ANSI
         color code interpretation by the Jansi library. This requires
         org.fusesource.jansi:jansi:1.8 on the class path.  Note that
         Unix-based operating systems such as Linux and Mac OS X
         support ANSI color codes by default. -->
    <withJansi>true</withJansi>
    <encoder>
      <pattern>[%thread] %highlight(%-5level) %cyan(%logger{15}) - %msg %n</pattern>
    </encoder>
  </appender>
  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
输出：
```
[main] WARN  c.l.TrivialMain - a warning message 0
[main] DEBUG c.l.TrivialMain - hello world number1
[main] DEBUG c.l.TrivialMain - hello world number2
[main] INFO  c.l.TrivialMain - hello world number3
[main] DEBUG c.l.TrivialMain - hello world number4
[main] WARN  c.l.TrivialMain - a warning message 5
[main] ERROR c.l.TrivialMain - Finish off with fireworks
```
它需要几行代码才能创建着色转换词。 标题为创建自定义转换说明符的部分讨论了在配置文件中注册转换字所需的步骤。

### 评价者
如上所述，当需要基于一个或多个EventEvaluator对象动态行为的转换说明符时，选项列表才派上用场。 EventEvaluator对象有责任确定给定的日志记录事件是否与评估者的条件匹配。

让我们回顾一个涉及EventEvaluator的例子。下一个配置文件将日志事件输出到控制台，显示日期，线程，级别，消息和调用者数据。鉴于提取日志记录事件的调用者数据的代价是昂贵的，我们只会在记录请求来自特定记录器时以及消息包含特定字符串时才会这样做。因此，我们确保只有特定的日志记录请求才会生成并显示其调用者信息。在其他情况下，如果调用者数据是多余的，我们不会惩罚应用程序的性能。

评估者，特别是评估表达式在过滤器章节的专门章节中介绍，如果您想以任何有意义的方式使用评估器，则必须阅读这些章节。另请注意，下面的示例隐式基于需要Janino库的JaninoEventEvaluator。请参阅设置文档的相应部分。
##### callerEvaluatorConfig.xml
```
<configuration>
  <evaluator name="DISP_CALLER_EVAL">
    <expression>logger.contains("chapters.layouts") &amp;&amp; \
      message.contains("who calls thee")</expression>
  </evaluator>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
    <encoder>
      <pattern>
        %-4relative [%thread] %-5level - %msg%n%caller{2, DISP_CALLER_EVAL}
      </pattern>
    </encoder>
  </appender>

  <root level="DEBUG"> 
    <appender-ref ref="STDOUT" /> 
  </root>
</configuration>
```
上述评估表达式匹配来自记录器的事件，其名称中包含字符串“chapters.layouts”，而消息包含字符串“谁召唤你”。 由于XML编码规则，＆字符不能按原样写入，因此需要将其转义为＆amp ;.

以下课程利用了上述配置文件中提到的一些特性。

```
package chapters.layouts;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.joran.JoranConfigurator;
import ch.qos.logback.core.joran.spi.JoranException;
import ch.qos.logback.core.util.StatusPrinter;

public class CallerEvaluatorExample {

  public static void main(String[] args)  {
    Logger logger = LoggerFactory.getLogger(CallerEvaluatorExample.class);
    LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();

    try {
      JoranConfigurator configurator = new JoranConfigurator();
      configurator.setContext(lc);
      configurator.doConfigure(args[0]);
    } catch (JoranException je) {
      // StatusPrinter will handle this
    }
    StatusPrinter.printInCaseOfErrorsOrWarnings(lc);

    for (int i = 0; i < 5; i++) {
      if (i == 3) {
        logger.debug("who calls thee?");
      } else {
        logger.debug("I know me " + i);
      }
    }
  }
}
```
上面的应用程序没有什么特别花哨。 发出五个日志请求，第三个发出“谁打给你？”的消息。

发出日志记录请求时，会评估相应的日志记录事件。只有第三个日志记录事件符合评估标准，才会显示其调用者数据。对于其他记录事件，评估标准不匹配，也不打印呼叫者数据。

人们可以改变表达以符合现实世界的情况。例如，可以结合记录器名称和请求级别。因此，记录来自应用程序敏感部分的WARN级别以上的请求，例如，一个金融交易模块将显示其主叫方数据。

重要提示：使用调用者转换字时，当表达式计算结果为真时，会输出调用者数据。

让我们考虑一下不同的情况。当日志请求中包含异常时，它们的堆栈跟踪也会输出。但是，有人可能想要为某些特定的异常压缩堆栈跟踪。

下面显示的Java代码创建三个日志请求，每个请求都有一个例外。第二个异常与其他异常不同：它包含字符串“do not display this”，它的类型为chapters.layouts.TestException。作为它的消息命令，让我们现在阻止打印第二个异常。

##### ExceptionEvaluatorExample.java
```
package chapters.layouts;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.joran.JoranConfigurator;
import ch.qos.logback.core.joran.spi.JoranException;
import ch.qos.logback.core.util.StatusPrinter;

public class ExceptionEvaluatorExample {

  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(ExceptionEvaluatorExample.class);
    LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();

    try {
      JoranConfigurator configurator = new JoranConfigurator();
      configurator.setContext(lc);
      lc.reset();
      configurator.doConfigure(args[0]);
    } catch (JoranException je) {
       // StatusPrinter will handle this
    }
    StatusPrinter.printInCaseOfErrorsOrWarnings(lc);

    for (int i = 0; i < 3; i++) {
      if (i == 1) {
        logger.debug("logging statement " + i, new TestException(
            "do not display this"));
      } else {
        logger.debug("logging statement " + i, new Exception("display"));
      }
    }
  }
}
```
在下一个配置文件中，评估表达式匹配包含可抛出的类型chapters.layouts.TextException的事件，这正是我们想要抑制的异常的类型。

##### exceptionEvaluatorConfig.xml
```
<configuration>

  <evaluator name="DISPLAY_EX_EVAL">
    <expression>throwable != null &amp;&amp; throwable instanceof  \
      chapters.layouts.TestException</expression>
  </evaluator>
        
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n%ex{full, DISPLAY_EX_EVAL}</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
通过此配置，每次在记录请求中包含chap.layouts.TestException实例时，堆栈跟踪都将被抑制。

### Creating a custom conversion specifier
...
### HTMLLayout
...
### Log4j XMLLayout
...