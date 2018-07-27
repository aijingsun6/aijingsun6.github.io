### 1.create process 
1.  ```erlang:spawn```
2.  ```proc_lib:spawn```
3.  ```erlang:spawn_link```
4.  ```proc_lib:spawn_link```


### 2.link and monitor
线程A与线程B相连接（link）那么任何一方退出(exit)另外一方会收到消息（双向）
```
{'EXIT',PID,Reason}
eg:
{'EXIT',<0.69.0>, normal}
```

线程A监视（monitor）线程B,只有线程B退出后，线程A才会收到消息
```
 {'DOWN',Ref,process, Pid, Reason}

eg:
 {'DOWN',#Ref<0.4255434239.1376256001.43098>,process, <0.69.0>, normal}

```




