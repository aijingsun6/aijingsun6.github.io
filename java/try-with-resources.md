# try-with-resources 语法
1.  try-with-resources 语法是1.7之后特定的语法。所谓的资源(resource)必须实现```java.lang.AutoCloseable```或者``` java.io.Closeable```接口
2.  try-with-resources 语法提供了一些便捷，不用在try之后添加finally
3.  try-with-resources 语法支持多个resource

###举个例子
```
该例子是在jdk1.7之前的语法
String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();// 1
    } finally {
        if (br != null) br.close(); //2
    }
}
```
这种语法有两种弊端
1.每个资源都要配合一个finally
2.在finally语句块中如果发生异常，就会抛出。但是实际情况下我们期待它是正常的，所以必须在finally语句块中必须添加 try...

### 使用try-with-resources语法
```
String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
    try( BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}
```
