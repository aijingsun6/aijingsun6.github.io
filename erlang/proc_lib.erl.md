# proc_lib.erl

#### 1.proc_lib.erl 是什么，在哪个应用内?
在stdlib应用内，是除了erlang.erl之外构建process的工具模块

#### 2.proc_lib构建的process和erlang构建的process有什么差别？
```erlang:spawn```构建的process在运行失败的时候没有rb记录，
但是```proc_lib:spawn```构建的process在运行失败或者出错的时候会有rb记录

#### 3.```proc_lib:spawn```实现原理是什么?

上源码
```
spawn(F) when is_function(F) ->
    Parent = get_my_name(),
    Ancestors = get_ancestors(),
    erlang:spawn(?MODULE, init_p, [Parent,Ancestors,F]).
...

init_p(Parent, Ancestors, Fun) when is_function(Fun) ->
    put('$ancestors', [Parent|Ancestors]),
    Mfa = erlang:fun_info_mfa(Fun),
    put('$initial_call', Mfa),
    try
	Fun()
    catch
	Class:Reason ->
	    exit_p(Class, Reason, erlang:get_stacktrace())
    end.
```




