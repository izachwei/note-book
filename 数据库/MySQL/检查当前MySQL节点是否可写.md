## 检查当前MySQL节点是否可写（主节点）

### 利用 @@read_only 

执行SQL `SELECT @@read_only `，返回 result = 1 表示只读节点

### 利用更新语句

执行任意更新语句，抛出 `DataAccessException` 异常则代表当前节点为只读节点。

