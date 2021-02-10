
# BeanFactoryPostProcessor

在实例化所有Bean之前会执行 `BeanFactoryPostProcessors`，实现该接口，可以在 Spring 的 Bean 创建之前，修改 Bean的定义属性 等 // TODO “还有什么功能”

```java
package org.springframework.beans.factory.config;

import org.springframework.beans.BeansException;

/**
 * Allows for custom modification of an application context's bean definitions,
 * adapting the bean property values of the context's underlying bean factory.
 *
 * <p>Application contexts can auto-detect BeanFactoryPostProcessor beans in
 * their bean definitions and apply them before any other beans get created.
 *
 * <p>Useful for custom config files targeted at system administrators that
 * override bean properties configured in the application context.
 *
 * <p>See PropertyResourceConfigurer and its concrete implementations
 * for out-of-the-box solutions that address such configuration needs.
 *
 * <p>A BeanFactoryPostProcessor may interact with and modify bean
 * definitions, but never bean instances. Doing so may cause premature bean
 * instantiation, violating the container and causing unintended side-effects.
 * If bean instance interaction is required, consider implementing
 * {@link BeanPostProcessor} instead.
 *
 * @see BeanPostProcessor
 * @see PropertyResourceConfigurer
 */
public interface BeanFactoryPostProcessor {

	/**
	 * Modify the application context's internal bean factory after its standard
	 * initialization. All bean definitions will have been loaded, but no beans
	 * will have been instantiated yet. This allows for overriding or adding
	 * properties even to eager-initializing beans.
	 * @param beanFactory the bean factory used by the application context
	 * @throws org.springframework.beans.BeansException in case of errors
	 */
	void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}

```



【注意】`BeanFactoryPostProcessor` 是在 Spring 容器 加载了 Bean 的定义文件之后，在 Bean 实例化 之前执行的。接口方法的入参是 `ConfigurrableListableBeanFactory` ，使用该参数，可以获取到相关 Bean 的定义信息，例子：



## Read More

- [spring之扩展点](https://blog.csdn.net/windsunmoon/article/details/44283585)
- [Spring的BeanFactoryPostProcessor和BeanPostProcessor接口的区别](https://blog.csdn.net/qq_17612199/article/details/53115446)