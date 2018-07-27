# gen_server.erl

##### gen_server的启动过程

```
1. gen_server:start_link(Name, Mod, Args, Options)

2. gen:start(gen_server, link, Name, Mod, Args, Options).

3.  proc_lib:start_link(gen, init_it,[gen_server, self(), self(), Name, Mod, Args, Options],
    Time,
    spawn_opts(Options))

阻塞等待

回调： gen:init_it ->
       gen_server:init_it-> 给3中的阻塞进程发消息，该线程结束初始化
       Mod:init(Args)->
       gen_server:loop ->
       proc_lib:hibernate(?MODULE,wake_hib,...)
       回调：
         gen_server:wake_hib ->
         gen_server:decode_msg->


```
在回调结束```Mod:init(..) -> {ok, State}```之后process进入```hibernate```状态


##### gen_server hibernate 的好处
```
Puts the calling process into a wait state where its memory allocation has been reduced as much as possible. This is useful if the process does not expect to receive any messages soon.
```

##### gen_server如何处理消息
```
gen_server:call(Process, Request,Timeout) ->

gen:call(Process, '$gen_call', Request,Timeout)->

erlang:send(Process, {'$gen_call', {self(), Mref}, Request}, [noconnect])

...

gen_server:wake_hib(...)
gen_server:decode_msg(...)
gen_server:handle_msg(...) 
回调： Mod:handle_call(...) -> 
   reply:
   reply({To, Tag}, Reply) ->
    catch To ! {Tag, Reply}.

继续 loop(...)

```

##### gen_server的超时处理
以下是主要处理逻辑,主要处理demonitor
```
catch erlang:send(Process, {Label, {self(), Mref}, Request}, [noconnect]),
      receive
        {Mref, Reply} ->
          erlang:demonitor(Mref, [flush]),
          {ok, Reply};
        {'DOWN', Mref, _, _, noconnection} ->
          Node = get_node(Process),
          exit({nodedown, Node});
        {'DOWN', Mref, _, _, Reason} ->
          exit(Reason)
      after Timeout ->
        erlang:demonitor(Mref, [flush]),
        exit(timeout)
      end
```

##### gen_server如何进行休眠  Option = [{hibernate_after,HibernateAfterTimeout}..]

如果确定该gen_server消息不多，负载较弱，可以适当的加入休眠的选项