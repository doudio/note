> #### znsd springboot springdata jpa

# SpringData JPA

##### JPA

 JPA是Java Persistence API的简称，Java持久层API，将系统中的数据进行持久化存储所依仗的API，JPA支持XML及注解两种形式来描述对象与表之间的关系

##### spring data jpa

 Spring Data JPA 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套JPA应用框架，可使开发者用极简的代码即可实现对数据的访问和操作。

##### springBoot+springdata jpa

- 添加依赖

```xml
  <!--引入JPA的依赖关系 -->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
```

- 在application.properties中添加配置

```properties
spring.jpa.database=mysql
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
```

- 实体类添加注解(跟Hibernate一样)

```java
@Entity
@Table(name="t_user")
public class User implements Serializable{
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private int id;

    @Column
    private String name;
    
}
```

- DAO层接口继承JpaRepository

```java
@Repository
public interface UserDao extends JpaRepository<User, Integer>{
    // User ：表示本接口所操作的实体对象是User
    // Integer ：表示User的主键类型
}
```

##### 自动生成查询语句

```java
// 发下方法无需dao实现，JPA封装通用的方法
List<T> findAll();
List<T> findAll(Sort sort);
List<T> findAllById(Iterable<ID> ids);
List<S> saveAll(Iterable<S> entities);
void flush();
S saveAndFlush(S entity);
void deleteInBatch(Iterable<T> entities);
void deleteAllInBatch();
T getOne(ID id);
List<S> findAll(Example<S> example);
List<S> findAll(Example<S> example, Sort sort);
```

##### 自定义简单查询

- 根据方法名生成查询语句，如：findXXBy、queryXXBy、countXXBy

```java
// 该方法会自动生成select * from t_user t where t.name = #name#的SQL语句
User findByName(String name);
```

- 添加and/or查询条件

```java
// 该方法会自动生成select * from t_user t where t.name = #name# or t.sex = #sex# 
User findByNameOrSex(String name);
```

- 关键词的简单使用

```java
// LIKE ,生成select * from t_user t where t.namek like #name#
User findByNameLike(String name);
// order by:生成select * from t_user t where t.name = #name# order by age
User findByNameOrderByAge(String name);
// between :生成select * from t_user t where t.age bewtenn #age1# and #age2#
User findByAgeBetween(int age1,int age2);
```

##### 关键字对照表

| Keyword           | Sample                                  | JPQL snippet                                                 |
| ----------------- | --------------------------------------- | ------------------------------------------------------------ |
| And               | findByLastnameAndFirstname              | … where x.lastname = ?1 and x.firstname = ?2                 |
| Or                | findByLastnameOrFirstname               | … where x.lastname = ?1 or x.firstname = ?2                  |
| Is,Equals         | findByFirstnameIs,findByFirstnameEquals | … where x.firstname = ?1                                     |
| Between           | findByStartDateBetween                  | … where x.startDate between ?1 and ?2                        |
| LessThan          | findByAgeLessThan                       | … where x.age < ?1                                           |
| LessThanEqual     | findByAgeLessThanEqual                  | … where x.age ⇐ ?1                                           |
| GreaterThan       | findByAgeGreaterThan                    | … where x.age > ?1                                           |
| GreaterThanEqual  | findByAgeGreaterThanEqual               | … where x.age >= ?1                                          |
| After             | findByStartDateAfter                    | … where x.startDate > ?1                                     |
| Before            | findByStartDateBefore                   | … where x.startDate < ?1                                     |
| IsNull            | findByAgeIsNull                         | … where x.age is null                                        |
| IsNotNull,NotNull | findByAge(Is)NotNull                    | … where x.age not null                                       |
| Like              | findByFirstnameLike                     | … where x.firstname like ?1                                  |
| NotLike           | findByFirstnameNotLike                  | … where x.firstname not like ?1                              |
| StartingWith      | findByFirstnameStartingWith             | … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith        | findByFirstnameEndingWith               | … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing        | findByFirstnameContaining               | … where x.firstname like ?1 (parameter bound wrapped in %)   |
| OrderBy           | findByAgeOrderByLastnameDesc            | … where x.age = ?1 order by x.lastname desc                  |
| Not               | findByLastnameNot                       | … where x.lastname <> ?1                                     |
| In                | findByAgeIn(Collection ages)            | … where x.age in ?1                                          |
| NotIn             | findByAgeNotIn(Collection age)          | … where x.age not in ?1                                      |
| TRUE              | findByActiveTrue()                      | … where x.active = true                                      |
| FALSE             | findByActiveFalse()                     | … where x.active = false                                     |
| IgnoreCase        | findByFirstnameIgnoreCase               | … where UPPER(x.firstame) = UPPER(?1)                        |

##### 复杂查询

- 分页查询

```java
// JPA已经实现分布查询的功能，只需在方法中加入Pageable参数
User findByName(String name,Pageable page);// 该方法表示将查询出来的结果分布展示
// 设置Pageable对象的相关属性便可分页
```

- 限制查询：查询方法的结果可以通过使用`first`或`top`关键字来限制

```java
// 查询第一条数据
User findFirstByName(String name,Pageable page);
// 查询前10条
List<User> findFirst10ByName(String name,Pageable page);
List<User> findFirst10ByName(String name, Sort sort);
```

- 使用注解@Query自定义SQL

```java
// 查询
@Query("select * from t_user t where t.name = #name#")
User findByName(String name);

// 修改、删除
@Transactional    //对方法添加事务管理
@Modifying        //声明该方法为修改数据
@Query("delete from t_user where id = ?1")
void deleteById(Long id);
```

> 在某些情况`@Query`封装sql会报错, 可以使用本地sql
>
> `@Query("HQL")` : 封装简单的 HQL 语句
>
> `@Query(value = "MySql", nativeQuery = true)` : 封装本地`sql`

##### 多表查询

- 与hibernate一致