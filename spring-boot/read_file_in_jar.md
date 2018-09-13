# read file in class path in jar
### 错误方式
```
File file = ResourceUtils.getFile("classpath:templates");
```
这种情况，在idea情况下是可用的，但是一旦打包成 jar文件之后,就会报错找不到该文件
```
cannot be resolved to absolute file path because it does not reside in the file system: jar:file:/D:/proj/gitlab/lilith-auth/auth_upload/build/libs/auth_upload-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/menu.json
...
```

### 正确方式
```
//  @Autowired
// ApplicationContext context

Resource res = this.context.getResource(String.format("classpath:%s",MENU_FILE));
InputStream is = (res.getInputStream();
...

```