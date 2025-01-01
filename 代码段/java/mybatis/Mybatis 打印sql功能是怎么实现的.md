# Mybatis 打印sql功能是怎么实现的



控制台显示

```
2025-01-01 14:24:09.258 DEBUG 1302 --- [  XNIO-1 task-2] c.b.b.d.s.m.B.selectList                 : ==>  Preparing: SELECT ancestor,descendant FROM bs_category_path WHERE (distance = ? AND is_deleted = ?)
2025-01-01 14:24:09.260 DEBUG 1302 --- [  XNIO-1 task-2] c.b.b.d.s.m.B.selectList                 : ==> Parameters: 1(Integer), 0(Long)
2025-01-01 14:24:09.265 DEBUG 1302 --- [  XNIO-1 task-2] c.b.b.d.s.m.B.selectList                 : <==      Total: 6
```

来源于org.apache.ibatis.logging.jdbc.ConnectionLogger

```java
@Override
public Object invoke(Object proxy, Method method, Object[] params)
    throws Throwable {
  try {
    if (Object.class.equals(method.getDeclaringClass())) {
      return method.invoke(this, params);
    }
    if ("prepareStatement".equals(method.getName()) || "prepareCall".equals(method.getName())) {
      if (isDebugEnabled()) {
        debug(" Preparing: " + removeExtraWhitespace((String) params[0]), true);
      }
      PreparedStatement stmt = (PreparedStatement) method.invoke(connection, params);
      stmt = PreparedStatementLogger.newInstance(stmt, statementLog, queryStack);
      return stmt;
    } else if ("createStatement".equals(method.getName())) {
      Statement stmt = (Statement) method.invoke(connection, params);
      stmt = StatementLogger.newInstance(stmt, statementLog, queryStack);
      return stmt;
    } else {
      return method.invoke(connection, params);
    }
  } catch (Throwable t) {
    throw ExceptionUtil.unwrapThrowable(t);
  }
}
```



箭头来源于 org.apache.ibatis.logging.jdbc.BaseJdbcLogger

```java
private String prefix(boolean isInput) {
  char[] buffer = new char[queryStack * 2 + 2];
  Arrays.fill(buffer, '=');
  buffer[queryStack * 2 + 1] = ' ';
  if (isInput) {
    buffer[queryStack * 2] = '>';
  } else {
    buffer[0] = '<';
  }
  return new String(buffer);
}
```

