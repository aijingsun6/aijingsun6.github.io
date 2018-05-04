# Hashtable 源码解析
### Hashtable 特性
-  线程安全
-  K,V都不能是null
-  K的必须hashCode,equals方法，为了防止冲突
-  本质上存储结构是 数组+单链表
### 存储结构示例
```
| 0 |
| 1 | -> |entry| -> |entry|
| 2 |
.
.
.
数组的下标是hashCode % length
同一链表的所有的键都具有相同的hashCode,为了防止冲突
```
