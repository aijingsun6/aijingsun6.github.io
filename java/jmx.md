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
