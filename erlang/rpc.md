# RPC

### rpc 是什么？
远程调用

### rpc进程名称是什么，是什么时候创建的？
```rex```
kernel创建
kernel.erl
```
 Rpc = #{id => rex,
                    start => {rpc, start_link, []},
                    restart => permanent,
                    shutdown => 2000,
                    type => worker,
                    modules => [rpc]},
...
```

### rpc的热点是什么？
A1 -> A0

A2 -> A0

那么A0很容易形成热点，而且是以节点(node)为单位的堵塞

### rpc:call 与 rpc:block_call的区别
```
# rpc:call

handle_call({call, Mod, Fun, Args, Gleader}, To, S) ->
    handle_call_call(Mod, Fun, Args, Gleader, To, S);
...
handle_call_call(Mod, Fun, Args, Gleader, To, S) ->
    %% Spawn not to block the rpc server.
    {Caller,_} =
	erlang:spawn_monitor(
	  fun () ->
		  set_group_leader(Gleader),
		  Reply = 
		      %% in case some sucker rex'es 
		      %% something that throws
		      case catch apply(Mod, Fun, Args) of
			  {'EXIT', _} = Exit ->
			      {badrpc, Exit};
			  Result ->
			      Result
		      end,
		  gen_server:reply(To, Reply)
	  end),
    {noreply, maps:put(Caller, To, S)}.

```
```
# rpc:block_call
handle_call({block_call, Mod, Fun, Args, Gleader}, _To, S) ->
    MyGL = group_leader(),
    set_group_leader(Gleader),
    Reply = 
	case catch apply(Mod,Fun,Args) of
	    {'EXIT', _} = Exit ->
		{badrpc, Exit};
	    Other ->
		Other
	end,
    group_leader(MyGL, self()), % restore
    {reply, Reply, S};

```
所以rpc:call其实算是异步调用，切记不要使用rpc:bloack_call

### 如何优化rpc?
##### 1.尽量使用 gen_server:call({PID,Node}, Request, Timeout)来代替rpc:call(M,F,A)
##### 2.尽量自己建立类似于rex的进程，然后使用负载
##### 3.如果使用的是cast，请尽量合并请求，减少消息数量