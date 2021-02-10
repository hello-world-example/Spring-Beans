# beans.factory.Aware

`Aware` 是一个标记接口，没有任何方法签名，在 Bean 的声明周期中，通过 `BeanPostProcessor` 给 Bean 传入声明周期过程中的对象

```java
/**
 * @link org.springframework.beans.factory.config.BeanPostProcessor
 * @link org.springframework.context.support.ApplicationContextAwareProcessor
 * @since 3.1
 */
public interface Aware { }
```

## 常用 Aware 简介

### context.support.ApplicationContextAwareProcessor

`ApplicationContextAwareProcessor` 处理以下 `Aware`

- `EnvironmentAware` ： 设置 **`Environment`** （`ConfigurableEnvironment`）
- `EmbeddedValueResolverAware` ： 设置 `StringValueResolver`（`EmbeddedValueResolver`）
- `ResourceLoaderAware` ： 设置 `ResourceLoaderAware`（`ConfigurableApplicationContext`）
- **`ApplicationEventPublisherAware`** ： 设置 **`ApplicationEventPublisher`**（`ConfigurableApplicationContext`）
- `MessageSourceAware` ： 设置 `MessageSourceAware`（`ConfigurableApplicationContext`）
- **`ApplicationContextAware`** ： 设置 **`ApplicationContext`**（`ConfigurableApplicationContext`）

```java
private void invokeAwareInterfaces(Object bean) {
  // env 和 系统属性
  if (bean instanceof EnvironmentAware) {
    ((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
  }
  // 属性解析器
  if (bean instanceof EmbeddedValueResolverAware) {
    ((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
  }
  // Bean 的 资源扫描器 
  if (bean instanceof ResourceLoaderAware) {
    ((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
  }
  // 事件发布
  if (bean instanceof ApplicationEventPublisherAware) {
    ((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
  }
  // 国际化
  if (bean instanceof MessageSourceAware) {
    ((MessageSourceAware) bean).setMessageSource(this.applicationContext);
  }
  // 容器
  if (bean instanceof ApplicationContextAware) {
    ((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
  }
}
```

### factory.support.AbstractAutowireCapableBeanFactory

- `BeanNameAware`
- `BeanClassLoaderAware`
- `BeanFactoryAware`

```java
protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
  ... 
    // 调用 
    // BeanNameAware
    // BeanClassLoaderAware
    // BeanFactoryAware
    invokeAwareMethods(beanName, bean);
  ...
    // BeanPostProcessor 的 postProcessBeforeInitialization 方法
    // ❤❤❤ context.support.ApplicationContextAwareProcessor 也在这一步被调用
    // EnvironmentAware
    // EmbeddedValueResolverAware
    // ResourceLoaderAware
    // ApplicationEventPublisherAware
    // MessageSourceAware
    // ApplicationContextAware
    wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
  ...
    // InitializingBean 的 afterPropertiesSet 方法
    invokeInitMethods(beanName, wrappedBean, mbd);
  ...
    // BeanPostProcessor 的 postProcessAfterInitialization 方法
    wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
  ...
  return wrappedBean;
}

private void invokeAwareMethods(final String beanName, final Object bean) {
  if (bean instanceof Aware) {
    // 设置 Bean 的名称
    if (bean instanceof BeanNameAware) {
      ((BeanNameAware) bean).setBeanName(beanName);
    }
    // 设置加载 Bean 的 ClassLoader
    if (bean instanceof BeanClassLoaderAware) {
      ClassLoader bcl = getBeanClassLoader();
      if (bcl != null) {
        ((BeanClassLoaderAware) bean).setBeanClassLoader(bcl);
      }
    }
    // 设置 BeanFactory（ AbstractAutowireCapableBeanFactory ）
    if (bean instanceof BeanFactoryAware) {
      ((BeanFactoryAware) bean).setBeanFactory(AbstractAutowireCapableBeanFactory.this);
    }
  }
}
```



### @since 3.1 context.annotation.ImportAware

`ImportAware` 常与 `@Configuration` 、 `@Import` 配合使用，获取指定注解信息，根据注解的配置设置 Bean。

#### @EnableAsync 示例

```java
@Configuration
public abstract class AbstractAsyncConfiguration implements ImportAware {

  @Nullable
  protected AnnotationAttributes enableAsync;

  ...

    @Override
    public void setImportMetadata(AnnotationMetadata importMetadata) {
    this.enableAsync = AnnotationAttributes.fromMap( importMetadata.getAnnotationAttributes(EnableAsync.class.getName(), false));
    if (this.enableAsync == null) {
      throw new IllegalArgumentException( "@EnableAsync is not present on importing class " + importMetadata.getClassName());
    }
  }
  ...
}

@Configuration
@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
public class ProxyAsyncConfiguration extends AbstractAsyncConfiguration {

  @Bean(name = TaskManagementConfigUtils.ASYNC_ANNOTATION_PROCESSOR_BEAN_NAME)
  @Role(BeanDefinition.ROLE_INFRASTRUCTURE)
  public AsyncAnnotationBeanPostProcessor asyncAdvisor() {
    Assert.notNull(this.enableAsync, "@EnableAsync annotation metadata was not injected");
    AsyncAnnotationBeanPostProcessor bpp = new AsyncAnnotationBeanPostProcessor();
    bpp.configure(this.executor, this.exceptionHandler);
    Class<? extends Annotation> customAsyncAnnotation = this.enableAsync.getClass("annotation");
    if (customAsyncAnnotation != AnnotationUtils.getDefaultValue(EnableAsync.class, "annotation")) {
      bpp.setAsyncAnnotationType(customAsyncAnnotation);
    }
    // 获取注解中的配置
    bpp.setProxyTargetClass(this.enableAsync.getBoolean("proxyTargetClass"));
    bpp.setOrder(this.enableAsync.<Integer>getNumber("order"));
    return bpp;
  }

}
```

#### 使用示例

```java
@EnableAsync
public class Main {
  public static void main(String[] args) {

    AnnotationMetadata introspect = AnnotationMetadata.introspect(Main.class);
    Map<String, Object> annotationAttributes = introspect.getAnnotationAttributes(EnableAsync.class.getName());
    System.out.println(annotationAttributes);

    AnnotationAttributes attributes = AnnotationAttributes.fromMap(annotationAttributes);
    Number order = attributes.getNumber("order");
    System.out.println(order);
  }
}

```



### web.context.support.ServletContextAwareProcessor

- `ServletContextAware`
- `ServletConfigAware`

这两对象搭配使用，可以基于 Servlet 变成，注册 Servlet、Filter 等

```java
@Override
public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
  // javax.servlet.ServletContext
  if (getServletContext() != null && bean instanceof ServletContextAware) {
    ((ServletContextAware) bean).setServletContext(getServletContext());
  }
  // javax.servlet.ServletConfig
  if (getServletConfig() != null && bean instanceof ServletConfigAware) {
    ((ServletConfigAware) bean).setServletConfig(getServletConfig());
  }
  return bean;
}
```



## 小结

|执行顺序|简介|模块|
|---|----|----|
|1. BeanNameAware|获取 Bean 的名称|spring-beans|
|2. BeanClassLoaderAware|获取 Bean 的 ClassLoader|spring-beans|
|3. **BeanFactoryAware**|获取 `BeanFactory` 容器|spring-beans|
|4. **EnvironmentAware**|获取 Environment|spring-contxt|
|5. EmbeddedValueResolverAware|获取 配置信息|spring-contxt|
|6. ResourceLoaderAware|获取 ResourceLoader，加载扫描资源|spring-contxt|
|7. **ApplicationEventPublisherAware**|获取 事件发布器|spring-contxt|
|8. MessageSourceAware|获取 `MessageSource`，处理国际化|spring-contxt|
|9. **ApplicationContextAware**|获取 `ApplicationContext` 容器|spring-contxt|
|10. **ServletContextAware**|获取 `ServletContext` Web 容器|spring-contxt|
|11. **ServletConfigAware**|获取 Servlet 配置信息|spring-contxt|
|12. ImportAware|获取注解配置|spring-contxt|



