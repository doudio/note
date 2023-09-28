> #### znsd springboot i18n

> 添加配置类

```java
@Configuration
public class ConfigSpringMVC implements WebMvcConfigurer {

	@Bean  
    public LocaleResolver localeResolver() {  
        SessionLocaleResolver slr = new SessionLocaleResolver();  
        slr.setDefaultLocale(Locale.US);  
        return slr;  
    }
	
    @Bean
    public LocaleChangeInterceptor localeChangeInterceptor() {
        LocaleChangeInterceptor lci = new LocaleChangeInterceptor();
        lci.setParamName("i18n");
        return lci;
    }

    @Bean
    public MessageSource messageSource() {
    	ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
    	messageSource.setBasename("international/i18n");
    	return messageSource;
    }
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(localeChangeInterceptor());
    }

}
```

> 页面获取值并转换

```html
<a href="/thymeleaf/emps?i18n=en_US">English(US)</a>
<a href="/thymeleaf/emps?i18n=zh_CN">简体中文</a>

[[#{i18n.text}]]
```



