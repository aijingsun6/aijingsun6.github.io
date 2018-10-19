## 绑定挂载
自Docker早期以来，绑定坐骑一直存在。 与卷相比，绑定装载具有有限的功能。 使用绑定装入时，主机上的文件或目录将装入容器中。 文件或目录由其在主机上的完整路径或相对路径引用。 相反，当您使用卷时，会在主机上的Docker存储目录中创建一个新目录，Docker会管理该目录的内容。

该文件或目录不需要已存在于Docker主机上。 如果它尚不存在，则按需创建。 绑定挂载非常高效，但它们依赖于具有特定目录结构的主机文件系统。 如果您正在开发新的Docker应用程序，请考虑使用命名卷。 您无法使用Docker CLI命令直接管理绑定装入。

### 使用 -v | --mount 命令
最初，-v或--volume标志用于独立容器，而--mount标志用于swarm服务。 但是，从Docker 17.06开始，您还可以将--mount与独立容器一起使用。 通常， - mount更明确，更冗长。 最大的区别是-v语法将所有选项组合在一个字段中，而--mount语法将它们分开。 以下是每个标志的语法比较。

#### -v

```
-v $SRC:$DST:$RW
RW: ro, consistent, delegated, cached, z, and Z
```

#### --mount
```
--mount 'k=v,k2=v2,k3=v3[...]'
type=$TYPE, $TYPE:bind, volume, or tmpfs
source=$SOURCE
destination=$DST
readonly
bind-propagation:rprivate, private, rshared, shared, rslave, slave
consistent:consistent, delegated, or cached
z|Z   于修改selinux标签
```
#### -v ,--mount 区别
因为-v和--volume标志长期以来一直是Docker的一部分，所以它们的行为无法改变。 这意味着-v和--mount之间存在一种不同的行为。

如果使用-v或--volume绑定安装Docker主机上尚不存在的文件或目录，则-v会为您创建端点。 它始终作为目录创建。

如果使用--mount绑定安装Docker主机上尚不存在的文件或目录，则Docker不会自动为您创建它，但会生成错误。

#### 启动带有绑定挂载的容器
```
$ docker run -d \
  -it \
  --name devtest \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  nginx:latest
```
```
$ docker run -d \
  -it \
  --name devtest \
  -v "$(pwd)"/target:/app \
  nginx:latest
```
关闭容器
```
$ docker container stop devtest

$ docker container rm devtest
```
#### 挂载到容器上的非空目录中
```
$ docker run -d \
  -it \
  --name broken-container \
  --mount type=bind,source=/tmp,target=/usr \
  nginx:latest
```

```
$ docker run -d \
  -it \
  --name broken-container \
  -v /tmp:/usr \
  nginx:latest
```
#### 使用只读挂载
```
$ docker run -d \
  -it \
  --name devtest \
  --mount type=bind,source="$(pwd)"/target,target=/app,readonly \
  nginx:latest
```

```
$ docker run -d \
  -it \
  --name devtest \
  -v "$(pwd)"/target:/app:ro \
  nginx:latest
```

### 配置绑定选项
对于绑定装入和卷，绑定传播默认为rprivate。 它仅可用于绑定装入，并且仅适用于Linux主机。 绑定传播是一个高级主题，许多用户永远不需要配置它。
绑定传播是指在给定的绑定装载或命名卷中创建的装载是否可以传播到该装载的副本。 考虑一个挂载点/ mnt，它也安装在/ tmp上。 传播设置控制/ tmp / a上的挂载是否也可用于/ mnt / a。 每个传播设置都有一个递归对位。 在递归的情况下，考虑/ tmp / a也作为/ foo挂载。 传播设置控制是否存在/ mnt / a和/或/ tmp / a。


| 传播设置 | 描述 |
| --- | --- |
| shared | 原始安装的子安装将暴露给副本安装，副安装的副安装也会传播到原始安装。|
| slave |类似于共享安装，但只在一个方向上。 如果原始安装程序公开子安装，则副本安装程序可以看到它。 但是，如果副本装置公开子装载，则原始装载无法看到它。|
|private |其中的子安装不会暴露给副本安装，副安装的副安装不会暴露给原始安装。|
|rshared|与共享相同，但传播也扩展到嵌套在任何原始或副本装入点中的装入点。|
|rslave|与从属相同，但传播也延伸到嵌套在任何原始或副本装入点中的装入点|
|rprivate|默认值。 与private相同，意味着原始或副本装入点中任何位置的装载点都不会沿任一方向传播。|

```
$ docker run -d \
  -it \
  --name devtest \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  --mount type=bind,source="$(pwd)"/target,target=/app2,readonly,bind-propagation=rslave \
  nginx:latest
```
```
$ docker run -d \
  -it \
  --name devtest \
  -v "$(pwd)"/target:/app \
  -v "$(pwd)"/target:/app2:ro,rslave \
  nginx:latest

```