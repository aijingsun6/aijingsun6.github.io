# erlang 常见错误

### Bad value on output port 'efile'
[erlang-questions](http://erlang.org/pipermail/erlang-questions/2015-December/087231.html)

主要原因是在使用log4erl打印日志的时候，如果是
log4erl:info("~ts",[Arg]).
如果Arg包含有中文编码，就会出错，实验如下：

```
(myapp_1@127.0.0.1)4> log4erl:info("ok",[]).
ok
(myapp_1@127.0.0.1)5> log4erl:info("你是谁",[]).

=ERROR REPORT==== 2-Aug-2018::10:29:48 ===
Bad value on output port 'efile'


=ERROR REPORT==== 2-Aug-2018::10:29:48 ===
Bad value on output port 'efile'

ok

```