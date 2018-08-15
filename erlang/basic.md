# 基础知识

### 如何将源文件编码设置为utf-8
源文件的第一行为：
```%% coding: utf-8```
### 如何注释
使用字符```%```

### 基本数据类型
|名称|描述|示例| 如何判断|
|---|---|---|---|
|term| any data| ||
|number|||```erlang:is_number(X)```|
|atom|||```erlang:is_atom(X)```|
|binary|||```erlang:is_binary(X)```|
|reference|||```erlang:is_reference(X)```|
|function|||```erlang:is_function(X)```|
|port|||```erlang:is_port(X)```|
|pid|||```erlang:is_pid(X)```|
|tuple|||```erlang:is_tuple(X)```|
|map|||```erlang:is_map(X)```|
|list|||```erlang:is_list(X)```|
|string|like list|||
|record|||```erlang:is_record(X,rec_name)```|
|boolean|||```erlang:is_boolean(X)```|

### 基本数据类型的比较
```
number < atom < reference < fun < port < pid < tuple < map < nil < list < bit string
```

### 模块(module)
##### 模块的命名
```
-module(ModName). 
```
##### 已经定义的attr
|name|note|
|---|---|
|```-module(Module).```|module|
|```-export(Functions).```|functions|
|```-import(Module,Functions).```|import functions|
|```-compile(Options).```||
|```-vsn(Vsn).```|可以使用``` beam_lib:version/1 ```查看版本信息|
|```-on_load(Function).```||

### 函数
注意尾递归函数
