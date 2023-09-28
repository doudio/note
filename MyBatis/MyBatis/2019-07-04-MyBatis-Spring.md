> #### MyBatis Spring

### Spring整合mybatis的方式

- 采用MapperScannerConfigurer，它将会查找类路径下的映射器并自动将它们创建成MapperFactoryBean（Mybatis接口编程）
- 采用接口SqlSession的实现类SqlSessionTemplate
- 采用抽象类SqlSessionDaoSupport提供SqlSession

### 整合方式（一） 采用MapperScannerConfigurer，dao实现接口编程`推荐使用`

实现原理：MapperScannerConfigurer会查找类路径下的映射器并自动将它们创建成MapperFactoryBean，生成代理类

spring配置文件

```xml
<!-- mybatis session 工厂 -->
<bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="mapperLocations">
        <list>
<!-- 自动扫描mapper.xml文件 *文件夹下表示所有文件，也可以单独在mybatis-config.xml中单独配置-->
            <value>classpath*:/mybatis-mapping/*.xml</value>
        </list>
    </property>
    <property name="dataSource" ref="dataSource" />
</bean>
<!-- mybatis接口 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- mybatis 接口包（如果有多个可以用,逗号隔开） -->
    <property name="basePackage" value="com.znsd.mybatis.dao" />
    <!-- sqlSession工厂beanId -->
    <property name="sqlSessionFactoryBeanName" value="sessionFactory" />
    <!-- 指定dao层接口的注解 -->
    <property name="annotationClass" value="org.springframework.stereotype.Repository" />
</bean>

<!-- 定义事务管理器（声明式的事务） -->
<bean id="transactionManager"
      class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource" />
</bean>

<!-- 开启注解事务 -->
<tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true" />
```

> DAO接口

```java
@Repository
public interface StudentDao {
    public List<Student> select(Student student);
}
```

>  Mapper.xml映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- Mapper.xml中的namespace必须与@Repository注解的类名称一致 -->
<mapper namespace="com.znsd.ssm.dao.StudentDao">
    <select id="select" resultType="com.znsd.ssm.bean.Student">
        select * from t_student
    </select>
</mapper>
```

### 整合方式（二） MapperScannerConfigurer+注解

使用注解，不需要再配置映射文件，把mapperLocations注入去掉即可

```xml
<!-- mybatis session 工厂 -->
<bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!-- 使用注解，不需要再配置映射文件 -->
    <!-- <property name="mapperLocations">
        <list>
            <value>classpath*:/mybatis-mapping/*.xml</value>
        </list>
    </property> -->
    <property name="dataSource" ref="dataSource" />
</bean>
```

在dao层添加@select注解

```xml
@Repository
public interface StudentDao {
    @Select("select * from t_student")
    public List<Student> select(Student student);
}
```

### 整合方式（三） 采用SqlSessionTemplate

- 采用接口SqlSession的实现类SqlSessionTemplate来实现dao层的功能。 　　mybatis中, sessionFactory可由SqlSessionFactoryBuilder.来创建。MyBatis-Spring 中，使用了SqlSessionFactoryBean来替代。SqlSessionFactoryBean有一个必须属性dataSource，另外其还有一个通用属性configLocation（用来指定mybatis的xml配置文件路径）
- 配置sqlSessionTemplate，通过构造注入将sessionFactory注入给sqlSessionTemplate

```xml
  <!-- mybatis session 工厂 -->
  <bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="mapperLocations">
          <list>
              <!-- 自动扫描mapper.xml文件 *文件夹下表示所有文件，也可以单独在mybatis-config.xml中单独配置 -->
              <value>classpath*:/mybatis-mapping/*.xml</value>
          </list>
      </property>
      <property name="dataSource" ref="dataSource" />
  </bean>

  <!-- mybatis spring sqlSessionTemplate 使用是直接通过构造注入sessionFactory即可 -->
  <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
      <constructor-arg index="0" ref="sessionFactory"></constructor-arg>
  </bean>
```

- dao层实现类

```xml
  @Repository
  public class StudentDaoImpl implements StudentDao {

    // 自动注入配置的sqlSessionTemplate
    @Resource
    private SqlSessionTemplate sqlSessionTemplate;

    @Override
    public List<Student> select(Student student) {
        // 通过sqlSessionTemplate模版操作数据库的增、删、改、查
        return sqlSessionTemplate.selectList("com.znsd.ssm.dao.StudentDao.select", student);
    }
  }
```

### 整合方式（四） 采用SqlSessionDaoSupport

- SqlSessionDaoSupport类似于Spring为Hibernate提供的HibernateDaoSupport，Spring将SqlSession封装在SqlSessionDaoSupport中供子类使用
- 配置sqlSessionFactory

```xml
  <!-- mybatis session 工厂 -->
  <bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="mapperLocations">
          <list>
              <!-- 自动扫描mapper.xml文件 *文件夹下表示所有文件，也可以单独在mybatis-config.xml中单独配置 -->
              <value>classpath*:/mybatis-mapping/*.xml</value>
          </list>
      </property>
      <property name="dataSource" ref="dataSource" />
  </bean>

  <!-- 定义事务管理器（声明式的事务） -->
  <bean id="transactionManager"
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource" />
  </bean>

  <!-- 开启注解事务 -->
  <tx:annotation-driven transaction-manager="transactionManager"
                        proxy-target-class="true" />
```

- Dao实现类

```java
  @Repository
  public class StudentDaoImpl extends SqlSessionDaoSupport implements StudentDao {

    /**
     * 和HibernateDaoSupport一样，SqlSessionDaoSupport中需要生成sqlSession对象，
     * 可以重写setSqlSessionFactory方法将sqlSessionFactory注入给dao的父类生成sqlSession给子类使用
     */
    @Resource
    public void setSqlSessionFactory(SqlSessionFactory sqlSessionFactory) {
        // 必须提供SqlSessionFactory给父类，创建出SqlSession才能进行增删改查操作
        super.setSqlSessionFactory(sqlSessionFactory);
    }

    @Override
    public List<Student> select(Student student) {
        // 通过getSqlSession()方法获取sqlSession对象操作数据库的增、删、改、查
        return getSqlSession().selectList("com.znsd.ssm.dao.StudentDao.select", student);
    }
  }
```

### 总结

- Spring整合MyBatis的三种方法，最长用的方式为第一种`面向接口编程`
- classpath：只会到你的class路径中查找找文件; 
- classpath*：不仅包含class路径，还包括jar文件中(class路径)进行查找