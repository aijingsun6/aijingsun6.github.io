# [architecture](https://logback.qos.ch/manual/architecture.html)
### 2.1 Logback的架构
Logback的基本架构足够通用，以适用于不同的环境。 目前，logback分为三个模块，logback-core，logback-classic和logback-access。

核心模块为其他两个模块奠定了基础。 经典模块扩展了核心。 经典模块对应于log4j的显着改进版本。 Logback-classic本身实现了SLF4J API，因此您可以在Logback和其他日志记录系统（如JDK 1.4中引入的log4j或java.util.logging（JUL））之间来回切换。 第三个称为访问的模块与Servlet容器集成，以提供HTTP访问日志功能。 一个单独的文档涵盖访问模块文档。

在本文档的其余部分中，我们将编写“logback”来引用logback-classic模块。

### 2.2 Logger, Appenders and Layouts
Logback建立在三个主要类别上：Logger，Appender和Layout。 这三种类型的组件协同工作，使开发人员能够根据消息类型和级别记录消息，并在运行时控制这些消息的格式和报告位置。

Logger类是logback-classic模块的一部分。 另一方面，Appender和Layout接口是logback-core的一部分。 作为通用模块，logback-core没有记录器的概念。

#### 2.2.1 Logger context
他优先于任何日志API优于普通的System.out.println，它的优势在于能够禁用某些日志语句，同时允许其他人无阻碍地进行打印。 此功能假设记录空间（即所有可能的日志记录语句的空间）根据某些开发人员选择的标准进行分类。 在logback-classic中，这种分类是记录器的固有部分。 每一个记录器都附加到一个LoggerContext上，该LoggerContext负责制造记录器并将它们安排在层次结构树中。

例如，名为“com.foo”的记录器是名为“com.foo.Bar”的记录器的父亲。 同样，“java”是“java.util”的父项，“java.util.Vector”的祖先。 大多数开发人员应该熟悉这种命名方案。

根记录器驻留在记录器层次结构的顶部。 它在成立之初就是每个等级中的一部分，这是例外。 就像每个记录器一样，它可以通过它的名字进行检索，如下所示：
```
Logger rootLogger = LoggerFactory.getLogger(org.slf4j.Logger.ROOT_LOGGER_NAME);
```
所有其他记录器也可以使用org.slf4j.LoggerFactory类中的静态getLogger方法进行检索。 该方法将所需记录器的名称作为参数。 下面列出了Logger界面中的一些基本方法。
```
package org.slf4j; 
public interface Logger {

  // Printing methods: 
  public void trace(String message);
  public void debug(String message);
  public void info(String message); 
  public void warn(String message); 
  public void error(String message); 
}
```
#### 2.2.2 有效等级或等级继承
记录器可能会被分配级别。 在ch.qos.logback.classic.Level类中定义了一组可能的级别（TRACE，DEBUG，INFO，WARN和ERROR）。 请注意，在logback中，Level类是final的，不能被细分，因为标记对象的形式存在更灵活的方法。

如果一个给定的记录器没有被分配一个等级，那么它从它最接近的祖先继承一个等级。 更正式地说：

给定记录器L的有效等级等于其层次结构中的第一个非空等级，从L本身开始，并在层次结构中朝着根记录器向上进行。

为确保所有记录器最终可以继承一个级别，根记录器始终具有分配的级别。 默认情况下，这个级别是DEBUG。

以下是根据级别继承规则，具有各种分配的级别值以及由此产生的有效（继承）级别的四个示例。

##### Example 1

Logger name | Assigned level | Effective level
--- | --- | --- 
root|	DEBUG|	DEBUG
X	|none|	DEBUG
X.Y	|none|	DEBUG
X.Y.Z|none|	DEBUG

在上面的例子1中，只有根记录器被分配了一个级别。 该级别值DEBUG由其他记录器X，X.Y和X.Y.Z继承

##### Example 2

Logger name | Assigned level | Effective level
--- | --- | --- 
root|ERROR|ERROR
X	|INFO|INFO
X.Y	|DEBUG|DEBUG
X.Y.Z|WARN|	WARN

在上面的示例2中，所有记录器都有一个分配的级别值。 等级继承不起作用。

##### Example 3
Logger name | Assigned level | Effective level
--- | --- | --- 
root	|DEBUG	|DEBUG
X	|INFO	|INFO
X.Y	|none	|INFO
X.Y.Z	|ERROR	|ERROR

在上面的例子3中，记录器root，X和X.Y.Z分别分配了级别DEBUG，INFO和ERROR。 记录器X.Y从其父级X继承其级别值。

##### Example 4
Logger name | Assigned level | Effective level
--- | --- | --- 
root|DEBUG|DEBUG
X	|INFO|INFO
X.Y	|none|INFO
X.Y.Z|none|INFO
在上面的例子4中，记录器root和X分别分配了级别DEBUG和INFO。 记录器X.Y和X.Y.Z从最近的具有指定级别的父级X继承它们的级别值。

### 打印方法和基本选择规则
根据定义，打印方法确定记录请求的级别。 例如，如果L是记录器实例，则语句L.info（“..”）是INFO级别的记录语句。

如果日志请求的级别高于或等于其日志记录的有效级别，则说明该日志请求已启用。 否则，该请求被认为是被禁用的。 如前所述，没有指定级别的记录器将从其最近的祖先继承。 这条规则总结如下。

如果p> = q，则启用向具有有效级别q的记录器发布的级别为p的日志请求。

这条规则是logback的核心。 它假定级别的排序如下：TRACE <DEBUG <INFO <WARN <ERROR。

更为直观地说明，选择规则的工作原理如下。 在下表中，垂直标题显示记录请求的级别，由p指定，而水平标题显示记录器的有效级别，由q指定。 行（级别请求）和列（有效级别）的交集是基本选择规则产生的布尔值。

```
import ch.qos.logback.classic.Level;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
....

// get a logger instance named "com.foo". Let us further assume that the
// logger is of type  ch.qos.logback.classic.Logger so that we can
// set its level
ch.qos.logback.classic.Logger logger = (ch.qos.logback.classic.Logger) LoggerFactory.getLogger("com.foo");
//set its Level to INFO. The setLevel() method requires a logback logger
logger.setLevel(Level. INFO);

Logger barlogger = LoggerFactory.getLogger("com.foo.Bar");

// This request is enabled, because WARN >= INFO
logger.warn("Low fuel level.");

// This request is disabled, because DEBUG < INFO. 
logger.debug("Starting search for nearest gas station.");

// The logger instance barlogger, named "com.foo.Bar", 
// will inherit its level from the logger named 
// "com.foo" Thus, the following request is enabled 
// because INFO >= INFO. 
barlogger.info("Located nearest gas station.");

// This request is disabled, because DEBUG < INFO. 
barlogger.debug("Exiting gas station search");
```
### 检索记录器
调用具有相同名称的LoggerFactory.getLogger方法将始终返回对完全相同的记录器对象的引用。
```
Logger x = LoggerFactory.getLogger("wombat"); 
Logger y = LoggerFactory.getLogger("wombat");
```
x和y指的是完全相同的记录器对象。

因此，可以配置一个记录器，然后在代码中的其他位置检索相同的实例，而无需传递引用。与亲生父母基本相反，父母总是先于他们的孩子，可以按任意顺序创建和配置登录记录器。特别是，“父”记录器将查找并链接到它的后代，即使它在它们之后被实例化。

logback环境的配置通常在应用程序初始化时完成。首选的方法是读取配置文件。这种方法将在短期内讨论。

Logback通过软件组件可以很容易地命名记录器。这可以通过在每个类中实例化一个记录器来完成，记录器名称等于该类的完全限定名称。这是定义记录器的有用和直接的方法。由于日志输出带有生成记录器的名称，因此该命名策略可以轻松识别日志消息的来源。然而，这只是一种可能的，尽管是通用的命名记录器的策略。 Logback不限制可能的记录器集。作为一名开发人员，您可以随心所欲地指定记录器。

尽管如此，在他们所在的班级之后命名伐木者似乎是目前已知的最佳策略。

### Appenders and Layouts
根据记录器选择性启用或禁用记录请求的功能只是图片的一部分。 Logback允许记录请求打印到多个目的地。在logback中，输出目标被称为appender。目前，控制台，文件，远程套接字服务器，MySQL，PostgreSQL，Oracle和其他数据库，JMS以及远程UNIX系统日志守护程序都有appender。
可以将多个appender连接到记录器。

addAppender方法将appender添加到给定的记录器。给定记录器的每个启用的记录请求将被转发到该记录器中的所有appender以及层次结构中较高的appender。换句话说，appender是从记录器层次结构中累加性地继承的。例如，如果控制台appender添加到根日志记录器，则所有启用的日志记录请求将至少在控制台上打印。如果另外一个文件appender被添加到记录器中，比如说L，那么为L和L的孩子启用的记录请求将打印在文件和控制台上。可以重写此默认行为，以便通过将记录器的可加性标志设置为false来使appender累积不再具有可加性。

以下总结了Appender可加性的规则。
```
Appender可加性
记录器L的日志语句的输出将传递给L及其祖先中的所有appender。 这是“appender可加性”一词的含义。

但是，如果记录器L的祖先P表示加法性标记设置为假，则L的输出将被引导至L中的所有appender及其祖先直到并包括P，而不是任何祖先中的appenderP.

记录器默认情况下将其可加性标志设置为true。
```
通常情况下，用户不仅希望自定义输出目标，还希望定制输出格式。 这是通过将布局与appender关联来实现的。 该布局负责根据用户的意愿格式化日志记录请求，而appender负责将格式化的输出发送到其目的地。 PatternLayout是标准logback分发的一部分，它允许用户根据类似于C语言printf函数的转换模式指定输出格式。

例如，具有转换模式“％-4relative [％thread]％-5level％logger {32} - ％msg％n”的PatternLayout将输出类似于以下内容的内容：
```
176  [main] DEBUG manual.architecture.HelloWorld2 - Hello world.
```
第一个字段是程序启动以来经过的毫秒数。 第二个字段是进行日志请求的线程。 第三个字段是日志请求的级别。 第四个字段是与日志请求关联的记录器的名称。 ' - '后面的文本是请求的消息。

### 参数化日志记录
鉴于Logback-classic中的记录器实现SLF4J的记录器界面，某些打印方法允许使用多个参数。 这些打印方法的变体主要是为了提高性能，同时尽量减少对代码可读性的影响。

对于某些Logger记录器，编写，
```
logger.debug("Entry number: " + i + " is " + String.valueOf(entry[i]));
```
会产生构造消息参数的代价，即将整数i和entry[i]转换为字符串，并连接中间字符串。 这与消息是否被记录无关。

避免参数构建成本的一种可能方式是通过测试来记录日志语句。 这是一个例子。
```
if(logger.isDebugEnabled()) { 
  logger.debug("Entry number: " + i + " is " + String.valueOf(entry[i]));
}
```
这样，如果记录器禁用了调试功能，则不会产生参数构建的成本。 另一方面，如果记录器启用了DEBUG级别，则会产生两次评估记录器是否已启用的成本：一次在debugEnabled中，一次在调试中。 实际上，这种开销并不重要，因为评估记录器的时间少于实际记录请求所需时间的1％。

### 更好的选择
存在基于消息格式的便利替代方案。 假设条目是一个对象，你可以写：
```
Object entry = new SomeObject(); 
logger.debug("The entry is {}.", entry);
```
只有在评估是否记录之后，并且只有在决定是肯定的时候，记录器实现才会格式化消息并用条目的字符串值替换“{}”对。 换句话说，当日志语句被禁用时，这种形式不会产生参数构造的代价。

以下两行将产生完全相同的输出。 但是，如果禁用日志记录语句，则第二个变体的性能将比第一个变体至少高出30倍。
```
logger.debug("The new entry is "+entry+".");
logger.debug("The new entry is {}.", entry);
```
两个参数变体也是可用的。 例如，你可以写：
```
logger.debug("The new entry is {}. It replaces {}.", entry, oldEntry);
```
如果需要传递三个或更多参数，则Object []变体也可用。 例如，你可以写：
```
Object[] paramArray = {newVal, below, above};
logger.debug("Value {} was inserted between {} and {}.", paramArray);
```

### 引擎盖下窥视
在我们引入了重要的logback组件之后，我们现在准备好描述logback框架在用户调用记录器的打印方法时所采取的步骤。 现在让我们分析一下当用户调用名为com.wombat的记录器的info（）方法时logback需要执行的步骤。

#### 1.获取过滤器链决定
如果存在，则调用TurboFilter链。 Turbo过滤器可以设置上下文范围的阈值，也可以根据与每个日志记录请求关联的信息（例如标记，级别，记录器，消息或Throwable）过滤某些事件。 如果过滤器链的回复为FilterReply.DENY，则记录请求将被删除。 如果它是FilterReply.NEUTRAL，那么我们继续下一步，即步骤2.如果答复是FilterReply.ACCEPT，我们跳过下一步并直接跳到步骤3。

#### 2.应用基本选择规则
在此步骤中，logback会将记录器的有效级别与请求的级别进行比较。 如果根据此测试禁用了日志记录请求，则logback将丢弃该请求而不做进一步处理。 否则，它进入下一步。

#### 3.创建一个LoggingEvent对象
如果请求在先前的过滤器中存活，logback将创建一个ch.qos.logback.classic.LoggingEvent对象，其中包含请求的所有相关参数，例如请求的记录器，请求级别，消息本身， 可能已经传递了请求，当前时间，当前线程，关于发出日志请求的类的各种数据以及MDC。 请注意，这些字段中的一些字段只是在实际需要时才被初始化。 MDC用于使用其他上下文信息修饰日志记录请求。 MDC将在随后的章节中讨论。

#### 4.调用appender
创建LoggingEvent对象后，logback将调用所有适用appender的doAppend（）方法，即从记录器上下文继承的appender。

随logback发布一起提供的所有appender都会将实现doAppend方法的AppenderBase抽象类扩展到同步块中，以确保线程安全。 AppenderBase的doAppend（）方法也会调用连接到appender的自定义过滤器，如果有任何这样的过滤器存在的话。 可以动态附加到任何appender的自定义过滤器将在单独的章节中介绍。

#### 5. 格式化输出
被调用的appender负责格式化记录事件。 但是，一些（但不是全部）appender将格式化记录事件的任务委托给布局。 布局格式化LoggingEvent实例并将结果作为字符串返回。 请注意，一些appender（如SocketAppender）不会将日志记录事件转换为字符串，而是将其序列化。 因此，他们没有也不需要布局。

#### 6. 发送LoggingEvent
记录事件完全格式化后，每个appender将其发送到其目标。

这里是一个序列UML图，以显示一切如何工作。 您可能需要点击图片才能显示其较大的版本。

### 性能
经常引用的反对伐木的论据之一是其计算成本。 这是一个合理的问题，因为即使是中等规模的应用程序也可能产生数千个日志请求。 我们的大部分开发工作都用于测量和调整logback的性能。 独立于这些努力，用户仍应该了解以下性能问题。

#### 日志记录完全关闭时记录性能
您可以完全关闭日志记录，方法是将根记录器的级别设置为Level.OFF，即最高级别。 当日志记录完全关闭时，日志请求的成本由方法调用和整数比较组成。 在3.2Ghz Pentium D机器上，这个成本通常在20纳秒左右。

但是，任何方法调用都涉及参数构建的“隐藏”成本。 例如，对于某些记录器x书写，
```
x.debug("Entry number: " + i + "is " + entry[i]);
```
会产生构造消息参数的代价，即将整数i和entry[i]转换为字符串，并连接中间字符串，而不管消息是否被记录。

参数构建的成本可能相当高，并取决于所涉及参数的大小。 为了避免参数构建的成本，您可以利用SLF4J的参数化日志记录功能：
```
x.debug("Entry number: {} is {}", i, entry[i]);
```
这个变体不会产生参数构建的成本。 与之前调用debug（）方法相比，它的速度会更快。 只有当日志记录请求发送给附加的appender时，才会格式化消息。 而且，对消息格式化的组件进行了高度优化。

尽管上述将日志语句置于严格循环中，即非常频繁地调用代码，但它是一项双输入建议，可能会导致性能下降。 即使日志记录关闭，记录紧密循环也会减慢应用程序的运行速度，并且如果打开日志记录，将会产生大量（因此无用）的输出。

#### 打开日志记录时决定是否记录日志的性能
在logback中，不需要走记录器层次结构。 记录器在创建时知道其有效等级（即其等级，一旦考虑等级继承）。 如果父记录器的级别发生变化，则联系所有儿童记录器以注意变化。 因此，在接受或拒绝基于有效水平的请求之前，记录器可以做出准瞬时决定，而无需咨询其祖先。

#### 实际日志记录（格式化并写入输出设备）
这是格式化日志输出并将其发送到目标目标的成本。 再次，我们做了认真的努力，尽可能快地使布局（格式化程序）执行。 appender也是如此。 登录到本地计算机上的文件时，实际记录的典型成本大约为9到12微秒。 登录到远程服务器上的数据库时，它会上升到几毫秒。

虽然功能丰富，但logback的首要设计目标之一是执行速度，这是仅次于可靠性的要求。 一些logback组件已被重写多次以提高性能。