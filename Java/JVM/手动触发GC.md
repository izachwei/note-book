# 手动触发 GC
## 代码层面
```java
System.gc();
```

## Java 应用层面
```bash
jmap -histo:live pid
```
* 注：这个命令实际是用来统计展示当前堆内存区域存活的对象
*  `jmap -dump:live` 以及 `jmap -histo:live` 都会触发Full GC