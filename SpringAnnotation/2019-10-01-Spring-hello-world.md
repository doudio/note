> #### Spring hello world

1. 编写属性类`bean`
2. 编写`spring`配置文件类
3. 编写`main`方法驱动程序

> 编写属性类`bean`

```java
public class Student {

	private Integer id;
	private String name;
	
    // Getter(), Setter(), toString();
    
}
```

> 编写`spring`配置文件类

```java
/**
 * @desc <b>这是一个 Spring 配置类</b>
 * 
 * @author jiang ru yi
 * @time 2018年12月1日 : 下午5:41:31
 */
@Configuration
public class SpringCore {

	@Bean
	public Student student() {
		return new Student(110, "李四");
	}
		
}
```

> @Configuration 声明这是一个 Spring 配置类
>
> @Bean 给容器中添加组件
>
> 1. 默认 bean 的名字是方法名
>
> 2. 也可以通过 @bean : value 属性修改 bean 的名字
>
> 3. 注解的 initMethod 和 destroyMethod 属性可以指定初始化 消耗方法
>
> 4. 指定初始化 和 销毁方法可以: implements InitializingBean, DisposableBean
>
> 5. 通过 JSR250 注解来定义[初始化, 销毁] 方法 : 
>
>    1. @PostConstruct : 初始化
>    2. @PreDestroy : 销毁
>
> 6. 也可通过 Bean 的后置处理器: org.springframework.beans.factory.config.BeanPostProcessor
>
>    ```java
>    public interface BeanPostProcessor {
>    
>    	/**
>    	 * 前置
>    	 */
>    	@Nullable
>    	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
>    		return bean;
>    	}
>    
>    	/**
>    	 * 后置
>    	 */
>    	@Nullable
>    	default Object postProcessAfterInitialization(Object bean, 
>                                                      String beanName) 
>            									throws BeansException {
>    		return bean;
>    	}
>    
>    }
>    ```

> 编写`main`方法驱动程序

```java
public class SpringApplication {

	public static void main(String[] args) {
		ApplicationContext context = 
            new AnnotationConfigApplicationContext(SpringCore.class);
		
		Object bean = context.getBean("student");
		
		System.out.println(bean);
	}
	
}
```

> @ComponentScan() : 指定要扫描的包

> @Import(Classes.class) : 给容器中导入组件, ID默认为全类名
>
> >  若传入的类实现了 ImportSelector 接口, 该注解就会将 :selectImports 方法的返回值全部加入到容器中
>
> >  注意: 返回值不能为 null , String 是全类名
>
> ```java
> public class SelectImport implements ImportSelector {
> 
> 	public String[] selectImports(AnnotationMetadata metadata) {
> 		return new String[] {};
> 	}
> 
> }
> ```
>
> > 若传入类实现了 ImportBeanDefinitionRegistrar 接口
> >
> > 我们就可以通过: 方法入参的 registry 手动注册bean
>
> ```java
> public class ImportBean implements ImportBeanDefinitionRegistrar {
> 	/**
> 	 * @param registry 可以通过他手动的注册 Bean
> 	 */
> 	public void registerBeanDefinitions(
>         AnnotationMetadata importingClassMetadata, 
>         BeanDefinitionRegistry registry) {
> 		
> 	}   
> }
> ```
>
>

> @Scope() : 调整对象作用域
>
> * ConfigurableBeanFactory.SCOPE_PROTOTYPE
> * ConfigurableBeanFactory.SCOPE_SINGLETON
> * org.springframework.web.context.WebApplicationContext.SCOPE_REQUEST
> * org.springframework.web.context.WebApplicationContext.SCOPE_SESSION	
>
> value: 
>
> ​	prototype : 多实例的
>
> ​	singleton : 单实例的 (默认值)

> @Lazy() : 懒加载, 在单利的时候有效, 第一次获取时加载