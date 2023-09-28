> #### mybatis - plus - baseMapper

> `insert` `:` `操作`

```java
/** 插入一条记录
 * @param entity 实体对象
 * @return int 受影响的行数
 */
Integer insert(T entity);

/**插入一条记录
 * @param entity 实体对象
 * @return int 受影响的行数
 */
Integer insertAllColumn(T entity);
```

> `delete` `:` `操作`

```java
/** 根据 ID 删除
 * @param id 主键ID
 * @return int 受影响的行数
 */
Integer deleteById(Serializable id);

/** 根据 entity 条件，删除记录
 * @param wrapper 实体对象封装操作类（可以为 null）: new EntityWrapper<T>()
 * @return int 受影响的行数
 */
Integer delete(@Param("ew") Wrapper<T> wrapper);
```

