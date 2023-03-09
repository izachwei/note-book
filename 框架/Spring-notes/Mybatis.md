## Mybatis

### SqlSessionTemplate
* 具体执行sql操作的代理对象`sqlSessionProxy` , 使用 *JDK的动态代理*
```java
this.sqlSessionProxy = (SqlSession) newProxyInstance(SqlSessionFactory.class.getClassLoader(),
        new Class[] { SqlSession.class }, new SqlSessionInterceptor());

```
* 代理内容 获取一个SqlSession（DefalutSqlSession）, `TransactionSynchronizationManager` (事务管理器（Spring-tx))

* Mybatis 获取连接流程：
```Java

SqlSession -> executor -> SpringManagedTransaction.getConnection() -> DataSourceUtil

SqlSession -> SqlSessionInterceptor -> DefaultSqlSession -> 执行对应模板方法（query）-> MybatisCachingExecutor.query() -> BaseExecutor.query() -> MybatisSimpleExecutor.doQuery() -> BaseExecutor.getConnection() -> SpringManagedTransaction.getConnection() -> DataSourceUtil.getConnection() ->
	
	HikariPool(配置的数据库连接池)

```

```
private class SqlSessionInterceptor implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
      SqlSession sqlSession = getSqlSession(SqlSessionTemplate.this.sqlSessionFactory,
          SqlSessionTemplate.this.executorType, SqlSessionTemplate.this.exceptionTranslator);
      try {
        Object result = method.invoke(sqlSession, args);
        if (!isSqlSessionTransactional(sqlSession, SqlSessionTemplate.this.sqlSessionFactory)) {
          // force commit even on non-dirty sessions because some databases require
          // a commit/rollback before calling close()
          sqlSession.commit(true);
        }
        return result;
```
 * Spring 事务同步工具 TransactionSynchronizationManager 
 * 动态代理

 * ThreadLocal 存放 SqlSessionFatory 与当前线程获取SqlSessionHolder（其中存放SqlSessiond的引用）绑定 （前提当前SqlSession开启了事务）

#### Spring Hikari 默认配置
* CONNECTION_TIMEOUT （连接超时） 30s
* minIdle （最小线程数） 10
* maxPoolSize (最大线程数) 10
... 