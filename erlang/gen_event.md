# gen_event
### 启动过程
```
gen_event:start_link/1

gen:start(?MODULE, link, Name, ?NO_CALLBACK, [], []);

proc_lib:start_link(...)

回调：
gen:init_id/6
gen_event:init_id/6

gen_event:loop/6

gen_event:fetch_msg/6
等待消息...
```

### add_handler
```
gen_event:rpc(...)
gen:call()
erlang:send(Process, {Label, {self(), Mref}, Request}, [noconnect])
...

gen_event:fetch_msg
gen_event:handle_msg
gen_event:server_add_handler
回调
Mod:init(Args)
```

### notify
```
gen_event:send(M, {notify, Event}).
M ! {notify,Event}.

gen_event:fetch_msg
gen_event:handle_msg

gen_event:server_notify(Event, handle_event, MSL, ServerName).
gen_event:loop(..)
```