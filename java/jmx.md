# JMX (Java Management Extensions)

## JMX技术概述

Java Management Extensions（JMX）技术是Java平台标准版（Java SE平台）的标准部分。 JMX技术已添加到Java 2平台标准版（J2SE）5.0版本的平台中。

JMX技术提供了一种简单，标准的方式来管理应用程序，设备和服务等资源。由于JMX技术是动态的，因此您可以使用它来监视和管理资源的创建，安装和实施。您还可以使用JMX技术来监视和管理Java虚拟机（Java VM）。

JMX规范定义了Java编程语言中的体系结构，设计模式，API和服务，用于管理和监视应用程序和网络。

使用JMX技术，给定资源由一个或多个Java对象（称为Managed Beans或MBean）进行检测。这些MBean在核心管理的对象服务器中注册，称为MBean服务器。 MBean服务器充当管理代理程序，可以在大多数已为Java编程语言启用的设备上运行。

规范定义了用于管理已正确配置以进行管理的任何资源的JMX代理。 JMX代理由MBean服务器和一组用于处理MBean的服务组成，MBean服务器中注册了MBean。通过这种方式，JMX代理可以直接控制资源并使其可用于远程管理应用程序。

检测资源的方式完全独立于管理基础结构。因此，无论管理应用程序如何实现，都可以使资源易于管理。

JMX技术定义了标准连接器（称为JMX连接器），使您可以从远程管理应用程序访问JMX代理。使用不同协议的JMX连接器提供相同的管理接口。因此，无论使用何种通信协议，管理应用程序都可以透明地管理资源。只要这些系统或应用程序支持JMX代理，JMX代理也可以由不符合JMX规范的系统或应用程序使用。

## 为什么使用JMX技术
JMX技术为开发人员提供了一种灵活的方法来检测基于Java技术的应用程序（Java应用程序），创建智能代理，实现分布式管理中间件和管理器，并将这些解决方案顺利集成到现有的管理和监视系统中。

JMX技术使Java应用程序无需大量投资即可进行管理。
基于JMX技术的代理（JMX代理）可以在大多数支持Java技术的设备上运行。因此，Java应用程序可以变得易于管理，而对其设计几乎没有影响。 Java应用程序只需要嵌入一个托管对象服务器，并使其某些功能可用作在对象服务器中注册的一个或多个托管bean（MBean）。这就是从管理基础设施中受益所需的一切。
JMX技术提供了管理Java应用程序，系统和网络的标准方法。
例如，Java平台企业版（Java EE）5 Application Server符合JMX体系结构，因此可以使用JMX技术进行管理。
JMX技术可用于Java VM的开箱即用管理。
Java虚拟机（Java VM）使用JMX技术进行了高度检测。您可以启动JMX代理来访问内置Java VM检测，从而远程监视和管理Java VM。
JMX技术提供可扩展的动态管理架构。
每个JMX代理服务都是一个独立的模块，可以根据需要插入管理代理程序。这种基于组件的方法意味着JMX解决方案可以从小型设备扩展到大型电信交换机等。 JMX规范提供了一组核心代理服务。可以在管理基础架构中开发和动态加载，卸载或更新其他服务。
JMX技术利用现有的标准Java技术。
只要需要，JMX规范就会引用现有的Java规范，例如Java命名和目录接口（J.N.D.I.）API。
可以从NetBeans IDE模块创建基于JMX技术的应用程序（JMX应用程序）。
您可以从NetBeans更新中心（在NetBeans界面中选择工具 - >更新中心）获取模块，该模块使您可以使用NetBeans IDE创建JMX应用程序。这降低了JMX应用程序的开发成本。
JMX技术与现有管理解决方案和新兴技术相集成。
JMX API是任何管理系统供应商都可以实现的开放接口。 JMX解决方案可以使用查找和发现服务以及Jini网络技术和服务定位协议（SLP）等协议。


## JMX技术的架构
JMX技术可分为三个级别，如下所示：

- 仪表
- JMX代理
- 远程管理

### 仪表
要使用JMX技术管理资源，必须首先使用Java编程语言检测资源。您使用称为MBean的Java对象来实现对资源检测的访问。 MBean必须遵循JMX规范中定义的设计模式和接口。这样做可确保所有MBean以标准化方式提供受管资源工具。除了标准MBean之外，JMX规范还定义了一种称为MXBean的特殊类型的MBean。 MXBean是仅引用预定义数据类型集的MBean。存在其他类型的MBean，但这条路径将集中在标准MBean和MXBeans上。

一旦资源由MBean检测，就可以通过JMX代理进行管理。 MBean不需要了解它们将运行的JMX代理。

MBean设计灵活，简单且易于实施。应用程序，系统和网络的开发人员可以以标准方式管理其产品，而无需了解或投资复杂的管理系统。可以用最少的努力使现有资源易于管理。

此外，JMX规范的检测级别提供了通知机制。此机制使MBean能够生成通知事件并将其传播到其他级别的组件。

### JMX代理
基于JMX技术的代理（JMX代理）是一种标准管理代理，可直接控制资源并使其可用于远程管理应用程序。 JMX代理通常与它们控制的资源位于同一台机器上，但这种安排不是必需的。

JMX代理的核心组件是MBean服务器，这是一个在其中注册MBean的托管对象服务器。 JMX代理还包括一组用于管理MBean的服务，以及至少一个允许管理应用程序访问的通信适配器或连接器。

实现JMX代理时，您不需要知道它将管理的资源的语义或功能。事实上，JMX代理甚至不需要知道它将服务哪些资源，因为任何符合JMX规范的资源都可以使用任何提供资源所需服务的JMX代理。同样，JMX代理不需要知道将访问它的管理应用程序的功能。

### 远程管理
可以通过许多不同方式访问JMX技术工具，可以通过现有管理协议（如简单网络管理协议（SNMP））或通过专有协议进行访问。 MBean服务器依赖于协议适配器和连接器，以便从代理的Java虚拟机（Java VM）之外的管理应用程序访问JMX代理。

每个适配器都提供通过MBean服务器中注册的所有MBean的特定协议的视图。例如，HTML适配器可以在浏览器中显示MBean。

连接器提供管理器端接口，用于处理管理器和JMX代理之间的通信。每个连接器通过不同的协议提供相同的远程管理接口。当远程管理应用程序使用此接口时，无论协议如何，它都可以通过网络透明地连接到JMX代理。 JMX技术提供了一种标准解决方案，用于将JMX技术工具导出到基于Java远程方法调用（Java RMI）的远程应用程序。

## 监视和管理Java虚拟机
JMX技术还可用于监视和管理Java虚拟机（Java VM）。

Java VM具有内置检测功能，使您可以使用JMX技术监视和管理它。这些内置管理实用程序通常被称为Java VM的开箱即用管理工具。为了监视和管理Java VM的不同方面，Java VM包括一个平台MBean服务器和特殊的MXBeans，供符合JMX规范的管理应用程序使用。

Platform MXBeans和Platform MBean Server
平台MXBeans是一组MXBeans，随Java SE平台一起提供，用于监视和管理Java VM以及Java运行时环境（JRE）的其他组件。每个平台MXBean都封装了Java VM功能的一部分，例如类加载系统，即时（JIT）编译系统，垃圾收集器等。通过使用符合JMX规范的监视和管理工具，可以显示和交互这些MXBean，使您能够监视和管理这些不同的VM功能。一个这样的监视和管理工具是Java SE平台的JConsole图形用户界面（GUI）。

Java SE平台提供标准平台MBean服务器，其中注册了这些平台MXBean。平台MBean服务器还可以注册您要创建的任何其他MBean。

JConsole的
Java SE平台包括JConsole监视和管理工具，该工具符合JMX规范。 JConsole使用Java VM的广泛工具（平台MXBeans）来提供有关Java平台上运行的应用程序的性能和资源消耗的信息。

开箱即用的管理实践
由于实现JMX技术的标准监视和管理实用程序内置于Java SE平台中，因此您可以在不必编写单行JMX API代码的情况下查看现成的JMX技术。您可以通过启动Java应用程序然后使用JConsole对其进行监视来实现。

使用JConsole监视应用程序
此过程说明如何监视Notepad Java应用程序。在版本6之前的Java SE平台版本下，需要使用以下选项启动要使用JConsole监视的应用程序。

-Dcom.sun.management.jmxremote
但是，随Java SE 6平台提供的JConsole版本可以附加到支持Attach API的任何本地应用程序。换句话说，JACsole会自动检测在Java SE 6 HotSpot VM中启动的任何应用程序，而不需要使用上述命令行选项启动。

通过在终端窗口中使用以下命令启动Notepad Java应用程序：
java -jar
    JDK_HOME /演示/ JFC /记事本/ Notepad.jar
其中jdk_home是安装Java Development Kit（JDK）的目录。如果您没有运行Java SE平台的第6版，则需要使用以下命令：

java -Dcom.sun.management.jmxremote -jar
      JDK_HOME /演示/ JFC /记事本/ Notepad.jar
打开记事本后，在不同的终端窗口中，使用以下命令启动JConsole：
JConsole的
将显示“新建连接”对话框。

在“新建连接”对话框中，从“本地进程”列表中选择“Notepad.jar”，然后单击“连接”按钮。
JConsole打开并将自身连接到Notepad.jar进程。当JConsole打开时，您将看到与记事本相关的监视和管理信息的概述。例如，您可以查看应用程序正在使用的堆内存量，应用程序当前运行的线程数以及应用程序消耗的中央处理单元（CPU）容量。

单击不同的JConsole选项卡。
每个选项卡都提供有关运行记事本的Java VM的不同功能区域的更多详细信息。所有提供的信息都是从该踪迹中提到的各种JMX技术MXBeans中获得的。所有平台MXBeans都可以显示在MBeans选项卡中。 MBeans选项卡将在此跟踪的下一部分中进行检查。

要关闭JConsole，请选择Connection  - > Exit。

## MBeans介绍
MBean是一个托管Java对象，类似于JavaBeans组件，它遵循JMX规范中提出的设计模式。 MBean可以表示设备，应用程序或需要管理的任何资源。 MBean公开了一个由以下内容组成的管理界面：

- 一组可读或可写属性，或两者兼而有之。
- 一组可调用的操作。
- 自我描述。
管理接口在MBean实例的整个生命周期中不会更改。 MBean还可以在发生某些预定义事件时发出通知。

JMX规范定义了五种类型的MBean：

- 标准MBean
- 动态MBean
- 打开MBean
- 模型MBean
- MXBean的
此跟踪中的示例仅演示了最简单的MBean类型，即标准MBean和MXBeans。

### 标准 MBean
本节提供了一个简单的标准MBean示例。

通过编写名为SomethingMBean的Java接口和一个名为Something的Java类来定义标准MBean，该类实现该接口。 接口中的每个方法都定义MBean中的属性或操作。 默认情况下，每个方法都定义一个操作。 属性和操作是遵循某些设计模式的方法。 标准MBean由MBean接口和类组成。 MBean接口列出了所有公开属性和操作的方法。 该类实现此接口并提供已检测资源的功能。

以下部分将介绍标准MBean和管理MBean的简单JMX技术启用代理（JMX代理）的示例。

#### MBean接口
```
HelloMBean.java

package com.example; 
 
public interface HelloMBean { 
 
    public void sayHello(); 
    public int add(int x, int y); 
    
    public String getName(); 
     
    public int getCacheSize(); 
    public void setCacheSize(int size); 
} 

```
按照惯例，MBean接口采用实现它的Java类的名称，并添加后缀MBean。在这种情况下，接口称为HelloMBean。实现此接口的Hello类将在下一节中介绍。

根据JMX规范，除了可由MBean管理的应用程序调用的命名和类型操作之外，MBean接口还包含可读且可写的命名和类型属性。 HelloMBean接口声明了两个操作：Java方法add（）和sayHello（）。

HelloMBean声明了两个属性：Name是只读字符串，CacheSize是一个可以读写的整数。声明了getter和setter方法，以允许托管应用程序访问并可能更改属性值。根据JMX规范的定义，getter是任何不返回void且名称以get开头的公共方法。 getter使管理器能够读取属性的值，该属性的类型是返回对象的类型。 setter是任何公共方法，它接受一个参数，其名称以set开头。 setter使管理器能够在属性中写入一个新值，其类型与参数的类型相同。
#### MBean 实现
```
HelloMBeanImpl.java

package com.example; 
 
public class HelloMBeanImpl
    implements HelloMBean { 
    public void sayHello() { 
        System.out.println("hello, world"); 
    } 
     
    public int add(int x, int y) { 
        return x + y; 
    } 
     
    public String getName() { 
        return this.name; 
    }  
     
    public int getCacheSize() { 
        return this.cacheSize; 
    } 
     
    public synchronized void setCacheSize(int size) {
        ...
    
        this.cacheSize = size; 
        System.out.println("Cache size now " + this.cacheSize); 
    } 
    ...
     
    private final String name = "Reginald"; 
    private int cacheSize = DEFAULT_CACHE_SIZE; 
    private static final int 
        DEFAULT_CACHE_SIZE = 200; 
}
```

直截了当的Hello类提供了HelloMBean声明的操作和属性的定义。 sayHello（）和add（）操作非常简单，但实际操作可以根据需要简单或复杂。

还定义了获取Name属性以及获取和设置CacheSize属性的方法。在此示例中，Name属性值永远不会更改。但是，在实际情况中，此属性可能会随托管资源的运行而更改。例如，该属性可能表示正常运行时间或内存使用情况等统计信息。这里，该属性仅仅是Reginald的名称。

调用setCacheSize方法使您可以从其声明的默认值200更改CacheSize属性。在实际方案中，更改CacheSize属性可能需要执行其他操作，例如丢弃条目或分配新条目。此示例仅打印一条消息以确认缓存大小已更改。但是，可以定义更复杂的操作，而不是简单调用println（）。

通过如此定义的Hello MBean及其接口，它们现在可用于管理它们所代表的资源，如以下部分所示。

#### 创建JMX代理以管理资源
一旦资源由MBean检测，该资源的管理由JMX代理执行。

JMX代理的核心组件是MBean服务器。 MBean服务器是注册MBean的托管对象服务器。 JMX代理还包括一组用于管理MBean的服务。 有关MBean服务器实现的详细信息，请参阅MBeanServer接口的API文档。

 ```
 Main.java

 package com.example; 
 
import java.lang.management.*; 
import javax.management.*; 
 
public class Main { 
 
    public static void main(String[] args) 
        throws Exception { 
     
        MBeanServer mbs = ManagementFactory.getPlatformMBeanServer(); 
        ObjectName name = new ObjectName("com.example:type=Hello"); 
        Hello mbean = new Hello(); 
        mbs.registerMBean(mbean, name); 
          
        ...
     
        System.out.println("Waiting forever..."); 
        Thread.sleep(Long.MAX_VALUE); 
    } 
} 
 ```

 JMX代理Main首先通过调用java.lang.management.ManagementFactory类的getPlatformMBeanServer（）方法获取由平台创建和初始化的MBean服务器。如果平台尚未创建MBean服务器，则getPlatformMBeanServer（）通过调用JMX方法MBeanServerFactory.createMBeanServer（）自动创建MBean服务器。 Main获取的MBeanServer实例名为mbs。

接下来，Main定义它将创建的MBean实例的对象名称。每个JMX MBean都必须具有对象名称。对象名称是JMX类ObjectName的实例，必须符合JMX规范定义的语法。即，对象名称必须包含域和键属性列表。在Main定义的对象名称中，域是com.example（包含示例MBean的包）。此外，key-property声明此对象的类型为Hello。

创建名为mbean的Hello对象的实例。然后，通过将对象和对象名称传递给对JMX方法MBeanServer.registerMBean（）的调用，将名为mbean的Hello对象注册为MBean服务器mbs中具有对象名称的MBean。

在MBean服务器中注册了Hello MBean后，Main只是等待对Hello执行管理操作。在此示例中，这些管理操作正在调用sayHello（）和add（），以及获取和设置属性值。

#### 运行标准MBean示例
...


### MXBeans
本节介绍一种特殊类型的MBean，称为MXBeans。

MXBean是一种MBean，仅引用一组预定义的数据类型。通过这种方式，您可以确保任何客户端（包括远程客户端）都可以使用您的MBean，而无需客户端访问表示MBean类型的特定于模型的类。 MXBeans提供了将相关值捆绑在一起的便捷方式，而无需特别配置客户端来处理捆绑包。

与标准MBean的方式相同，MXBean是通过编写名为SomethingMXBean的Java接口和实现该接口的Java类来定义的。但是，与标准MBean不同，MXBeans不需要将Java类称为Something。接口中的每个方法都定义MXBean中的属性或操作。注释@MXBean也可用于注释Java接口，而不是要求接口的名称后跟MXBean后缀。

MXBeans存在于Java 2平台标准版（J2SE）5.0软件的java.lang.management包中。但是，除了java.lang.management中定义的标准集之外，用户现在还可以定义自己的MXBean。

MXBeans背后的主要思想是MXBean接口中引用的java.lang.management.MemoryUsage等类型，在本例中为java.lang.management.MemoryMXBean，它们被映射到一组标准类型，即所谓的Open包javax.management.openmbean中定义的类型。确切的映射规则出现在MXBean规范中。但是，一般原则是简单类型（如int或String）保持不变，而复杂类型（如MemoryUsage）则映射到标准类型CompositeDataSupport。

MXBean示例包含以下文件，这些文件位于jmx_examples.zip中：

QueueSamplerMXBean接口
实现MXBean接口的QueueSampler类
QueueSample由MXBean接口中的getQueueSample（）方法返回的Java类型
Main，设置和运行示例的程序
MXBean示例使用这些类来执行以下操作：

定义一个管理Queue <String>类型资源的简单MXBean
在MXBean中声明一个getter，getQueueSample，它在调用时获取队列的快照，并返回一个Java类QueueSample，它将以下值捆绑在一起：
拍摄快照的时间
队列大小
在给定时间排队的队长
在MBean服务器中注册MXBean

#### MXBean接口
```
QueueSamplerMXBean.java

package com.example; 
 
public interface QueueSamplerMXBean { 
    public QueueSample getQueueSample(); 
    public void clearQueue(); 
} 
```
请注意，您声明MXBean接口的方式与声明标准MBean接口的方式完全相同。 QueueSamplerMXBean接口声明了一个getter，getQueueSample和一个clearQueue操作。

#### 定义MXBean操作
```
QueueSampler.java

package com.example; 
 
import java.util.Date; 
import java.util.Queue; 
 
public class QueueSampler 
                implements QueueSamplerMXBean { 
     
    private Queue<String> queue; 
         
    public QueueSampler (Queue<String> queue) { 
        this.queue = queue; 
    } 
         
    public QueueSample getQueueSample() { 
        synchronized (queue) { 
            return new QueueSample(new Date(), 
                           queue.size(), queue.peek()); 
        } 
    } 
         
    public void clearQueue() { 
        synchronized (queue) { 
            queue.clear(); 
        } 
    } 
}

```

#### 定义MXBean接口返回的Java类型
```
QueueSample.java
package com.example; 
 
import java.beans.ConstructorProperties; 
import java.util.Date; 
 
public class QueueSample { 
     
    private final Date date; 
    private final int size; 
    private final String head; 
         
    @ConstructorProperties({"date", "size", "head"}) 
    public QueueSample(Date date, int size, 
                        String head) { 
        this.date = date; 
        this.size = size; 
        this.head = head; 
    } 
         
    public Date getDate() { 
        return date; 
    } 
         
    public int getSize() { 
        return size; 
    } 
         
    public String getHead() { 
        return head; 
    } 
}  
```
在QueueSample类中，MXBean框架调用QueueSample中的所有getter以将给定实例转换为CompositeData实例，并使用@ConstructorProperties批注从CompositeData实例重建QueueSample实例。

#### 在MBean Server中创建和注册MXBean
```
Main.java
package com.example; 
 
import java.lang.management.ManagementFactory; 
import java.util.Queue; 
import java.util.concurrent.ArrayBlockingQueue; 
import javax.management.MBeanServer; 
import javax.management.ObjectName; 
 
public class Main { 
 
    public static void main(String[] args) throws Exception { 
        MBeanServer mbs = 
            ManagementFactory.getPlatformMBeanServer(); 
                
        ...  
        ObjectName mxbeanName = new ObjectName("com.example:type=QueueSampler");
        
        Queue<String> queue = new ArrayBlockingQueue<String>(10);
        queue.add("Request-1");
        queue.add("Request-2");
        queue.add("Request-3");
        QueueSampler mxbean = new QueueSampler(queue);
        
        mbs.registerMBean(mxbean, mxbeanName);
                 
        System.out.println("Waiting..."); 
        Thread.sleep(Long.MAX_VALUE); 
    } 
} 
```
Main类执行以下操作：

- 获取平台MBean服务器。
- 为MXBean QueueSampler创建对象名称。
- 为QueueSampler MXBean创建要处理的Queue实例。
- 将Queue实例提供给新创建的QueueSampler MXBean。
- 以与标准MBean完全相同的方式在MBean服务器中注册MXBean。


### 通知

JMX API定义了一种机制，使MBean能够生成通知，例如，指示状态更改，检测到的事件或问题。

要生成通知，MBean必须实现接口NotificationEmitter或扩展NotificationBroadcasterSupport。 要发送通知，您需要构造类javax.management.Notification或子类（例如AttributeChangedNotification）的实例，并将实例传递给NotificationBroadcasterSupport.sendNotification。

每个通知都有一个来源。 源是生成通知的MBean的对象名称。

每个通知都有一个序列号。 当订单很重要时，此号码可用于订购来自同一来源的通知，并且存在以错误的顺序处理通知的风险。 序列号可以是零，但优选地，来自给定MBean的每个通知的数量增量。
```
Hello.java

package com.example;

import javax.management.*;

public class Hello
        extends NotificationBroadcasterSupport
        implements HelloMBean {

    public void sayHello() {
        System.out.println("hello, world");
    }

    public int add(int x, int y) {
        return x + y;
    }

    public String getName() {
        return this.name;
    }

    public int getCacheSize() {
        return this.cacheSize;
    }

    public synchronized void setCacheSize(int size) {
        int oldSize = this.cacheSize;
        this.cacheSize = size;

        System.out.println("Cache size now " + this.cacheSize);

        Notification n = new AttributeChangeNotification(this,
                                sequenceNumber++, System.currentTimeMillis(),
                                "CacheSize changed", "CacheSize", "int",
                                oldSize, this.cacheSize);

        sendNotification(n);
    }

    @Override
    public MBeanNotificationInfo[] getNotificationInfo() {
        String[] types = new String[]{
            AttributeChangeNotification.ATTRIBUTE_CHANGE
        };

        String name = AttributeChangeNotification.class.getName();
        String description = "An attribute of this MBean has changed";
        MBeanNotificationInfo info = 
                new MBeanNotificationInfo(types, name, description);
        return new MBeanNotificationInfo[]{info};
    }
    
    private final String name = "Reginald";
    private int cacheSize = DEFAULT_CACHE_SIZE;
    private static final int DEFAULT_CACHE_SIZE = 200;
    private long sequenceNumber = 1;
}
```

此Hello MBean实现扩展了NotificationBroadcasterSupport类。 NotificationBroadcasterSupport实现NotificationEmitter接口。

操作和属性的设置方式与标准MBean示例相同，但CacheSize属性的setter方法现在定义了oldSize的值。此值记录设置操作之前的CacheSize属性的值。

通知是从JMX类AttributeChangeNotification的实例n构造的，该实例扩展了javax.management.Notification。通知是根据以下信息在setCacheSize（）方法的定义内构造的。此信息作为参数传递给AttributeChangeNotification。

通知源的对象名称，即Hello MBean，由此表示
序列号，即sequenceNumber，设置为1并逐渐增加
时间戳
通知消息的内容
已更改的属性的名称，在本例中为CacheSize
已更改的属性类型
旧属性值，在本例中为oldSize
新属性值，在本例中为this.cacheSize
然后将通知n传递给NotificationBroadcasterSupport.sendNotification（）方法。

最后，定义MBeanNotificationInfo实例以描述MBean为给定类型的通知生成的不同通知实例的特征。在这种情况下，发送的通知类型是AttributeChangeNotification通知。

#### 运行MBean通知示例
您将使用JConsole与Hello MBean进行交互，这次是发送和接收通知。此示例需要Java SE平台的第6版。

1. 如果尚未执行此操作，请将jmx_examples.zip保存到work_dir目录中。
在终端窗口中使用以下命令解压缩示例类包。
2. 解压缩jmx_examples.zip
3. 编译work_dir目录中的示例Java类。
javac com / example / * .java
4. 启动主应用程序。
java com.example.Main
生成Main正在等待某事发生的确认。

5. 在同一台计算机上的另一个终端窗口中启动JConsole。
6. JConsole的将显示“新建连接”对话框，其中列出了可以连接的正在运行的JMX代理的列表。

在“新建连接”对话框中，从列表中选择com.example.Main，然后单击“连接”。
将显示平台当前活动的摘要。

7. 单击MBeans选项卡。
此面板显示当前在MBean服务器中注册的所有MBean。

8. 在左侧框架中，展开MBean树中的com.example节点。
您将看到由Hello创建并注册的示例MBean Hello。如果单击Hello，则会在MBean树中看到其Notifications节点。

9. 展开MBean树中Hello MBean的Notifications节点。
请注意，面板为空白。

10. 单击“订阅”按钮。
接收的当前通知数（0）显示在“通知”节点标签中。

11. 展开MBean树中Hello MBean的Attributes节点，并将CacheSize属性的值更改为150。
在启动Main的终端窗口中，将显示此属性更改的确认。请注意，“通知”节点中显示的已接收通知数已更改为1。

12. 再次展开MBean树中Hello MBean的Notifications节点。
将显示通知的详细信息。

13. 要关闭JConsole，请选择Connection  - > Exit。

### 远程管理
JMX API使您可以使用基于JMX技术的连接器（JMX连接器）执行资源的远程管理。 JMX连接器使远程基于Java技术的客户端可以访问MBean服务器。连接器的客户端导出与MBean服务器基本相同的接口。

JMX连接器由连接器客户端和连接器服务器组成。连接器服务器连接到MBean服务器并侦听来自客户端的连接请求。连接器客户端负责与连接器服务器建立连接。连接器客户端通常位于与连接器服务器不同的Java虚拟机（Java VM）中，并且通常在不同的计算机上运行。 JMX API定义了基于远程方法调用（RMI）的标准连接协议。此协议使您可以从远程位置将JMX客户端连接到MBean服务器中的MBean，并对MBean执行操作，就像操作是在本地执行一样。

Java SE平台提供了一种开箱即用的方法，可以使用JMX API的标准RMI连接器远程监控应用程序。开箱即用的RMI连接器自动公开应用程序以进行远程管理，而无需您自己创建专用的远程连接器服务器。通过使用正确的属性启动Java应用程序来激活开箱即用的远程管理代理。然后，与JMX技术兼容的监视和管理应用程序可以连接到这些应用程序并远程监视它们。

#### 通过JConsole公开资源以进行远程管理
如果使用现成的远程管理代理和现有的监视和管理工具（如JConsole），则使用JMX API公开Java应用程序以进行远程管理非常简单。

要公开远程管理应用程序，需要使用正确的属性启动它。 此示例显示如何公开Main JMX代理以进行远程管理。

安全考虑：
为简单起见，在此示例中禁用了身份验证和加密安全机制。 但是，在实际环境中实现远程管理时，应实现这些安全机制。 接下来是什么？ 提供指向其他JMX技术文档的指针，其中显示了如何激活安全性。

此示例需要Java SE平台的第6版。要远程监视Main JMX代理，请按照下列步骤操作：

1. 如果尚未执行此操作，请将jmx_examples.zip保存到work_dir目录中。
在终端窗口中使用以下命令解压缩示例类包。
2. 解压缩jmx_examples.zip
3. 编译work_dir目录中的示例Java类。
javac com / example / * .java
4. 启动Main应用程序，指定公开Main以进行远程管理的属性。 （对于Windows，使用克拉（^）而不是反斜杠（\）将长命令拆分为多行）：
java -Dcom.sun.management.jmxremote.port = 9999 \
     -Dcom.sun.management.jmxremote.authenticate = false \
     -Dcom.sun.management.jmxremote.ssl = false \
     com.example.Main
生成Main正在等待某事发生的确认。

5. 在另一台机器上的另一个终端窗口中启动JConsole：
JConsole的
将显示“新建连接”对话框，其中列出了可以在本地连接的正在运行的JMX代理。

6. 选择“远程进程”，然后在“远程进程”字段中键入以下内容：
主机名：9999
在此地址中，hostname是运行Main应用程序的远程计算机的名称，9999是开箱即用的JMX连接器将连接的端口号。

7. 单击连接。
显示Main正在运行的Java虚拟机（Java VM）的当前活动的摘要。

8. 单击MBeans选项卡。
此面板显示当前在远程MBean服务器中注册的所有MBean。

9. 在左侧框架中，展开MBean树中的com.example节点。
您会看到Main创建并注册的示例MBean Hello。如果单击Hello，则会在MBean树中看到其关联的Attributes和Operations节点，即使它在另一台计算机上运行。

10. 要关闭JConsole，请选择Connection  - > Exit。


#### 创建自定义JMX客户端
此路径中的前面课程向您展示了如何创建JMX技术MBean和MXBeans，以及如何使用JMX代理注册它们。 但是，前面的所有示例都使用了现有的JMX客户端JConsole。 本课将演示如何创建自己的自定义JMX客户端。

自定义JMX客户端的示例，客户端包含在jmx_examples.zip中。 此JMX客户端与前面课程中看到的相同MBean，MXBean和JMX代理进行交互。 由于Client类的大小，它将在以下部分中以块的形式进行检查。

##### 导入JMX远程API类
```
Client .java

package com.example;
...

import javax.management.remote.JMXConnector;
import javax.management.remote.JMXConnectorFactory;
import javax.management.remote.JMXServiceURL;

public class Client {
    ...
}
```
##### 创建通知侦听器
JMX客户端需要一个通知处理程序，以侦听和处理可能由在JMX代理的MBean服务器中注册的MBean发送的任何通知。 JMX客户端的通知处理程序是NotificationListener接口的实例，如下所示。
```
public static class ClientListener implements NotificationListener {

    public void handleNotification(Notification notification,
            Object handback) {
        echo("\nReceived notification:");
        echo("\tClassName: " + notification.getClass().getName());
        echo("\tSource: " + notification.getSource());
        echo("\tType: " + notification.getType());
        echo("\tMessage: " + notification.getMessage());
        if (notification instanceof AttributeChangeNotification) {
            AttributeChangeNotification acn =
                (AttributeChangeNotification) notification;
            echo("\tAttributeName: " + acn.getAttributeName());
            echo("\tAttributeType: " + acn.getAttributeType());
            echo("\tNewValue: " + acn.getNewValue());
            echo("\tOldValue: " + acn.getOldValue());
        }
    }
}   
```
此通知侦听器确定它收到的任何通知的来源，并检索通知中存储的信息。 然后，它根据接收的通知类型对通知信息执行不同的动作。 在这种情况下，当侦听器收到AttributeChangeNotification类型的通知时，它将通过调用AttributeChangeNotification方法getAttributeName，getAttributeType，getNewValue和getOldValue来获取已更改的MBean属性的名称和类型，以及其旧值和新值。

稍后在代码中创建一个新的ClientListener实例。

 
ClientListener listener = new ClientListener（）;


##### 创建RMI连接器客户端
Client类创建一个RMI连接器客户端，该客户端配置为连接到启动JMX代理Main时将启动的RMI连接器服务器。这将允许JMX客户端与JMX代理进行交互，就像它们在同一台机器上运行一样。

```

echo("\nCreate an RMI connector client and " +
    "connect it to the RMI connector server");
JMXServiceURL url = 
    new JMXServiceURL("service:jmx:rmi:///jndi/rmi://:9999/jmxrmi");
JMXConnector jmxc = JMXConnectorFactory.connect(url, null);
```

如您所见，客户端定义了一个名为url的JMXServiceURL，它表示连接器客户端期望找到连接器服务器的位置。此URL允许连接器客户端从运行在本地主机的端口9999上的RMI注册表中检索RMI连接器服务器存根jmxrmi，并连接到RMI连接器服务器。

通过这样识别的RMI注册表，可以创建连接器客户端。连接器客户端jmxc是JMXConnector接口的一个实例，由JMXConnectorFactory的connect（）方法创建。 connect（）方法在调用时传递参数url和null环境映射。

##### 连接到远程MBean服务器
在RMI连接到位后，JMX客户端必须连接到远程MBean服务器，以便它可以与远程JMX代理程序在其中注册的各种MBean进行交互。
```
MBeanServerConnection mbsc = 
    jmxc.getMBeanServerConnection();
```
然后通过调用JMXConnector实例jmxc的getMBeanServerConnection（）方法创建名为mbsc的MBeanServerConnection实例。

连接器客户端现在连接到由JMX代理创建的MBean服务器，并且可以注册MBean并对它们执行操作，连接对两端保持完全透明。

首先，客户端定义一些简单的操作来发现有关代理的MBean服务器中找到的MBean的信息。

```
echo("\nDomains:");
String domains[] = mbsc.getDomains();
Arrays.sort(domains);
for (String domain : domains) {
    echo("\tDomain = " + domain);
}
        
...
        
echo("\nMBeanServer default domain = " + mbsc.getDefaultDomain());

echo("\nMBean count = " +  mbsc.getMBeanCount());
echo("\nQuery MBeanServer MBeans:");
Set<ObjectName> names = 
    new TreeSet<ObjectName>(mbsc.queryNames(null, null));
for (ObjectName name : names) {
    echo("\tObjectName = " + name);
}
```
客户端调用MBeanServerConnection的各种方法，以获取运行不同MBean的域，MBean服务器中注册的MBean数，以及它发现的每个MBean的对象名。

##### 通过代理对远程MBean执行操作
客户端通过创建MBean代理，通过MBean服务器连接访问MBean服务器中的Hello MBean。 此MBean代理是客户端的本地代理，并模拟远程MBean。
```
ObjectName mbeanName = new ObjectName("com.example:type=Hello");
HelloMBean mbeanProxy = JMX.newMBeanProxy(mbsc, mbeanName, 
                                          HelloMBean.class, true);

echo("\nAdd notification listener...");
mbsc.addNotificationListener(mbeanName, listener, null, null);

echo("\nCacheSize = " + mbeanProxy.getCacheSize());

mbeanProxy.setCacheSize(150);

echo("\nWaiting for notification...");
sleep(2000);
echo("\nCacheSize = " + mbeanProxy.getCacheSize());
echo("\nInvoke sayHello() in Hello MBean...");
mbeanProxy.sayHello();

echo("\nInvoke add(2, 3) in Hello MBean...");
echo("\nadd(2, 3) = " + mbeanProxy.add(2, 3));

waitForEnterPressed();
```
MBean代理允许您通过Java接口访问MBean，允许您在代理上进行调用，而不必编写冗长的代码来访问远程MBean。 通过调用javax.management.JMX类中的方法newMBeanProxy（），向其传递MBean的MBeanServerConnection，对象名，MBean接口的类名和true，来表示代理必须表现，从而创建Hello的MBean代理。 作为NotificationBroadcaster。 JMX客户端现在可以执行Hello定义的操作，就好像它们是本地注册的MBean的操作一样。 JMX客户端还添加了通知侦听器并更改了MBean的CacheSize属性，以使其发送通知。

##### 通过代理对远程MXBeans执行操作
您可以使用与创建MBean代理完全相同的方式为MXBean创建代理。

```
ObjectName mxbeanName = new ObjectName ("com.example:type=QueueSampler");
QueueSamplerMXBean mxbeanProxy = JMX.newMXBeanProxy(mbsc, 
    mxbeanName,  QueueSamplerMXBean.class);
QueueSample queue1 = mxbeanProxy.getQueueSample();
echo("\nQueueSample.Date = " + queue1.getDate());
echo("QueueSample.Head = " + queue1.getHead());
echo("QueueSample.Size = " + queue1.getSize());
echo("\nInvoke clearQueue() in QueueSampler MXBean...");
mxbeanProxy.clearQueue();

QueueSample queue2 = mxbeanProxy.getQueueSample();
echo("\nQueueSample.Date = " +  queue2.getDate());
echo("QueueSample.Head = " + queue2.getHead());
echo("QueueSample.Size = " + queue2.getSize());
```

如上所示，要为MXBean创建代理，您所要做的就是调用JMX.newMXBeanProxy而不是newMBeanProxy。 MXBean代理mxbeanProxy允许客户端调用QueueSample MXBean的操作，就好像它们是本地注册的MXBean的操作一样。

##### 关闭连接
一旦JMX客户端获得了所需的所有信息并在远程JMX代理的MBean服务器上的MBean上执行了所有必需的操作，就必须关闭连接。

```
jmxc.close();
```
##### 运行自定义JMX客户端示例
此示例需要Java SE平台的第6版。要使用自定义JMX客户端客户端远程监视Main JMX代理，请按照下列步骤操作：

1. 如果尚未执行此操作，请将jmx_examples.zip保存到work_dir目录中。
在终端窗口中使用以下命令解压缩示例类包。
2. 解压缩jmx_examples.zip
3. 编译work_dir目录中的示例Java类。
javac com / example / * .java
4. 启动Main应用程序，指定公开Main以进行远程管理的属性：
java -Dcom.sun.management.jmxremote.port = 9999 \
     -Dcom.sun.management.jmxremote.authenticate = false \
     -Dcom.sun.management.jmxremote.ssl = false \
     com.example.Main
生成Main正在等待某事发生的确认。

5. 在另一个终端窗口中启动客户端应用程序：
java com.example.Client
显示已获取MBeanServerConnection的确认。

6. 按Enter键。
显示由Main启动的MBean服务器中注册的所有MBean的域。

7. 再次按Enter键。
将显示MBean服务器中注册的MBean数，以及所有这些MBean的对象名。显示的MBean包括在Java VM中运行的所有标准平台MXBeans，以及由Main在MBean服务器中注册的Hello MBean和QueueSampler MXBean。

8. 再次按Enter键。
Client MBA的操作由Client调用，结果如下：
将一个通知侦听器添加到客户端以侦听来自Main的通知。
CacheSize属性的值从200更改为150。
在启动Main的终端窗口中，显示CacheSize属性更改的确认。
在启动Client的终端窗口中，显示来自Main的通知，通知Client ClientSize属性更改。
Hello MBean的sayHello操作被调用。
在您启动Main的终端窗口中，显示消息“Hello world”。
调用Hello MBean的add操作，值为2和3作为参数。结果由客户显示。
9. 再次按Enter键。
客户端调用QueueSampler MXBean的操作，结果如下：

将显示QueueSample值的日期，头部和大小。
调用clearQueue操作。
10. 再次按Enter键。
客户端关闭与MBean服务器的连接，并显示确认。


### 结束
