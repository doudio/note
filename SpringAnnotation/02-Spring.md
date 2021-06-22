

> bean 工厂添加组件

1. 定义类实现: `FactoryBean`
2. 在类中编写业务
3. 将该类注入到 `Spring` 容器中

> 定义类实现: `FactoryBean` & 在类中编写业务

```java
public class UserFactory implements FactoryBean<Object> {

	/**
	 * @desc <b>返回给容器中的组件</b>
	 */
	public User getObject() throws Exception {
		return new Object();
	}

	/**
	 * @desc <b>组件的类型</b>
	 */
	public Class<?> getObjectType() {
		return Object.class;
	}
	
	/**
	 * @desc <b>组件在容器中是否单利</b>
	 */
	public boolean isSingleton() {
		return false;
	}

}
```


> 将该类注入到 `Spring` 容器中

```java
/**
 * @desc <b>这是一个 Spring 配置类</b>
 */
@Configuration
@Import({SelectImport.class, ImportBean.class})
public class SpringCore {

	/**
	 * @desc <b>工厂注入</b>
	 */
	@Bean
	public UserFactory userFactory() {
		return new UserFactory();
	}

}
```

> 获取对象可以通过: name 获取, 若想获取工厂对象实例: `&name`
>
> 获取bean工厂的前缀是在: `org.springframework.beans.factory.BeanFactory` 中定义的
>
> ```java
> public interface BeanFactory {
> 
> 	/**
> 	 * 获取 Bean 工厂的前缀	 
>      */
> 	String FACTORY_BEAN_PREFIX = "&";
> }
> ```

> @Conditional() : 注册bean时添加条件

```java
/**
 * 组件添加条件
 */
public class LinuxCondition implements Condition {

	/**
	 * 	Environment : getEnvironment() 						: 运行时环境
	 * 	ConfigurableListableBeanFactory : getBeanFactory() 	: bean 工厂
	 *  BeanDefinitionRegistry : getRegistry() 				: 获取bean注册类
	 */
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) 
    {
		Environment environment = context.getEnvironment();
		ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
		BeanDefinitionRegistry registry = context.getRegistry();
		
		String property = environment.getProperty("os.name");
		
		return property.equals("linux");
	}

}
```

> 导入外部资源文件: @PropertySource({"classpath:/info.properties"})
>
> 获取资源文件中的值: @Value("${user.name}")

```java
@Qualifier("beanId")/** 指定注入容器中Bean*/
@Autowired/** 默认先按照类型去容器中查找, 若有多个使用属性名为 ID 在容器中查找*/

@Primary/** 在 @Autowired 注入找到多个时默认优先注入该bean:在添加组件时声明*/
```

> @Resource() 和 @Inject() 可以基本实现 @Autowired 功能, 但是部分功能还是不支持的:Java定义的规范
>
> @Resource() 没有支持 @Primary 和 @Autowired(required = false)
>
> @Inject() 没有支持 @Autowired(required = false)

> XXXAware 接口获取容器中底层的组件

```java
private void invokeAwareInterfaces(Object bean) {
    if (bean instanceof Aware) {
        if (bean instanceof EnvironmentAware) {
            ((EnvironmentAware) bean)
            .setEnvironment(this.applicationContext.getEnvironment());
        }
        if (bean instanceof EmbeddedValueResolverAware) {
            ((EmbeddedValueResolverAware) bean)
            .setEmbeddedValueResolver(this.embeddedValueResolver);
        }
        if (bean instanceof ResourceLoaderAware) {
            ((ResourceLoaderAware) bean)
            .setResourceLoader(this.applicationContext);
        }
        if (bean instanceof ApplicationEventPublisherAware) {
            ((ApplicationEventPublisherAware) bean)
            .setApplicationEventPublisher(this.applicationContext);
        }
        if (bean instanceof MessageSourceAware) {
            ((MessageSourceAware) bean)
            .setMessageSource(this.applicationContext);
        }
        if (bean instanceof ApplicationContextAware) {
            ((ApplicationContextAware) bean)
            .setApplicationContext(this.applicationContext);
        }
    }
}
```

> 指定运行时环境添加Bean

```java
@Profile("default") 指定某个环境下才注册bean默认值: default

添加运行时参数: -Dspring.profiles.active
```

