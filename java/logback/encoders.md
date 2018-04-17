# [encoders](https://logback.qos.ch/manual/encoders.html)

## 什么是编码器
编码器负责将事件转换为字节数组，并将该字节数组写入OutputStream。编码器在0.9.2版的logback版本中引入。在以前的版本中，大多数appender依赖布局来将事件转换为字符串，并使用java.io.Writer将其写出。在以前版本的logback中，用户将在PatternAppender中嵌套一个PatternLayout。由于logback 0.9.19，FileAppender和子类期望编码器不再进行布局。

为什么突破性改变？

布局，如下一章所详细讨论的，只能将事件转换为字符串。而且，由于布局无法控制事件写出的时间，所以布局不能将事件聚合成批次。与编码器相比，它不仅可以完全控制写出的字节格式，还可以控制何时（以及如果）这些字节被写出。

目前，PatternLayoutEncoder是唯一真正有用的编码器。它只是包装一个PatternLayout，它完成大部分工作。因此，除了不必要的复杂性之外，编码器看起来似乎并没有多大用处。但是，我们希望随着新型强大编码器的出现，这种印象会发生变化。

### 编码器接口
编码器负责将传入事件转换为字节数组，并将生成的字节数组写入相应的OutputStream。 因此，编码器可以完全控制什么以及何时将字节写入由拥有的appender维护的OutputStream。 这是编码器接口：

```
package ch.qos.logback.core.encoder;

public interface Encoder<E> extends ContextAware, LifeCycle {

   /**
   * This method is called when the owning appender starts or whenever output
   * needs to be directed to a new OutputStream, for instance as a result of a
   * rollover.
   */
  void init(OutputStream os) throws IOException;

  /**
   * Encode and write an event to the appropriate {@link OutputStream}.
   * Implementations are free to defer writing out of the encoded event and
   * instead write in batches.
   */
  void doEncode(E event) throws IOException;


  /**
   * This method is called prior to the closing of the underling
   * {@link OutputStream}. Implementations MUST not close the underlying
   * {@link OutputStream} which is the responsibility of the owning appender.
   */
  void close() throws IOException;
}
```

正如你所看到的，Encoder接口由很少的方法组成，但令人惊讶的是可以用这些方法完成许多有用的事情。

### LayoutWrappingEncoder
在logback版本0.9.19之前，许多appender依赖Layout实例来控制日志输出的格式。 由于存在大量基于布局界面的代码，我们需要一种编码器与布局互操作的方式。 LayoutWrappingEncoder弥补了编码器和布局之间的差距。 它实现了编码器接口并封装了一个布局，它将委托将事件转换为字符串的工作。

以下是LayoutWrappingEncoder类的摘录，说明对包装布局实例的委派是如何完成的。

```
package ch.qos.logback.core.encoder;

public class LayoutWrappingEncoder<E> extends EncoderBase<E> {

  protected Layout<E> layout;
  private Charset charset;
 
   // encode a given event as a byte[]
   public byte[] encode(E event) {
     String txt = layout.doLayout(event);
     return convertToBytes(txt);
  }

  private byte[] convertToBytes(String s) {
    if (charset == null) {
      return s.getBytes();
    } else {
      return s.getBytes(charset);
    }
  } 
}
```
doEncode（）方法首先让包装的布局将传入的事件转换为字符串。 得到的文本字符串根据用户选择的字符集编码转换为字节。

### PatternLayoutEncoder
鉴于PatternLayout是最常用的布局，logback迎合了PatternLayoutEncoder这个常见的用例，它是LayoutWrappingEncoder的扩展，仅限于包装PatternLayout的实例。

从logback 0.9.19版本开始，每当FileAppender或其子类之一配置了PatternLayout时，就必须使用PatternLayoutEncoder。 这在logback错误代码的相关条目中进行了解释。

#### immediateFlush属性
从LOGBACK 1.2.0开始，immediateFlush属性是封装Appender的一部分。
#### Output pattern string as header
为了便于分析日志文件，logback可以将用于日志输出的模式插入日志文件的顶部。 该功能默认为禁用。 可以通过将相关PatternLayoutEncoder的outputPatternAsHeader属性设置为'true'来启用它。 这里是一个例子：

```
<appender name="FILE" class="ch.qos.logback.core.FileAppender"> 
  <file>foo.log</file>
  <encoder>
    <pattern>%d %-5level [%thread] %logger{0}: %msg%n</pattern>
    <outputPatternAsHeader>true</outputPatternAsHeader>
  </encoder> 
</appender>
```

```
#logback.classic pattern: %d [%thread] %-5level %logger{36} - %msg%n
2012-04-26 14:54:38,461 [main] DEBUG com.foo.App - Hello world
2012-04-26 14:54:38,461 [main] DEBUG com.foo.App - Hi again
```
