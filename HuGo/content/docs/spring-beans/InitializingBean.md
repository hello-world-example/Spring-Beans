# InitializingBean // TODO

定义在 spring Bean 初始化后 所做的操作方式 常见有三种：

1.  `@PostConstruct` 注解
2. Bean 实现 `InitializingBean` 接口
3. 在 xml 中 bean 标签 配置  `init-method` 属性 指定初始化方法

```java
package org.springframework.beans.factory;

/**
 * Interface to be implemented by beans that need to react once all their
 * properties have been set by a BeanFactory: for example, to perform custom
 * initialization, or merely to check that all mandatory properties have been set.
 *
 * <p>An alternative to implementing InitializingBean is specifying a custom
 * init-method, for example in an XML bean definition.
 * For a list of all bean lifecycle methods, see the BeanFactory javadocs.
 *
 * @see BeanNameAware
 * @see BeanFactoryAware
 * @see BeanFactory
 * @see org.springframework.beans.factory.support.RootBeanDefinition#getInitMethodName
 * @see org.springframework.context.ApplicationContextAware
 */
public interface InitializingBean {

	/**
	 * Invoked by a BeanFactory after it has set all bean properties supplied
	 * (and satisfied BeanFactoryAware and ApplicationContextAware).
	 * <p>This method allows the bean instance to perform initialization only
	 * possible when all bean properties have been set and to throw an
	 * exception in the event of misconfiguration.
	 * @throws Exception in the event of misconfiguration (such
	 * as failure to set an essential property) or if initialization fails.
	 */
	void afterPropertiesSet() throws Exception;

}

```



## Read More

- [源码解析：init-method、@PostConstruct、afterPropertiesSet孰先孰后](http://sexycoding.iteye.com/blog/1046993)

  

