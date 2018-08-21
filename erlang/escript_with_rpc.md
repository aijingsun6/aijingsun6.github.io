```
#!/usr/bin/env escript
-mode(compile).
main(["noname"]) ->
  io:format("~p,~p ~n",[node(),auth:get_cookie()]);
main([NodeS,CookS]) ->
   Node = erlang:list_to_atom(NodeS),
   Cook = erlang:list_to_atom(CookS),
   net_kernel:start([Node, longnames]),
   erlang:set_cookie(node(), Cook),
   io:format("~p,~p ~n",[node(),auth:get_cookie()]).

```

```
D:\proj\github\erlang-bench>escript demo.escript noname
nonode@nohost,nocookie

D:\proj\github\erlang-bench>escript demo.escript b@127.0.0.1 abc
'b@127.0.0.1',abc

```
