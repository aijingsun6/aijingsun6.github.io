## 临时挂载
卷和绑定挂载允许您在主机和容器之间共享文件，以便即使在容器停止后也可以保留数据。

如果你在Linux上运行Docker，你有第三个选择：tmpfs mounts。 使用tmpfs mount创建容器时，容器可以在容器的可写层之外创建文件。

与卷和绑定挂载相反，tmpfs挂载是临时的，并且仅保留在主机内存中。 当容器停止时，将删除tmpfs挂载，并且不会保留写在那里的文件。

### 临时挂载的限制
与卷和绑定挂载不同，您不能在容器之间共享tmpfs挂载。
只有在Linux上运行Docker时才能使用此功能。

```
$ docker run -d \
  -it \
  --name tmptest \
  --mount type=tmpfs,destination=/app \
  nginx:latest
```
```
$ docker run -d \
  -it \
  --name tmptest \
  --tmpfs /app \
  nginx:latest

```
| 选项| 描述|
| --- | --- |
| tmpfs-size | tmpfs的大小以字节为单位。 默认情况下无限制|
| tmpfs-mode |八进制中tmpfs的文件模式。 例如，700或0770.默认为1777或 全局可写。|

```
docker run -d \
  -it \
  --name tmptest \
  --mount type=tmpfs,destination=/app,tmpfs-mode=1770 \
  nginx:latest
```