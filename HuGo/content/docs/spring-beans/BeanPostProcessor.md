# BeanPostProcessor

`BeanPostProcessor` 提供对bean实例的操作扩展，**在 Spring容器 对 bean实例化 和 设置依赖 之后**，其回调开始执行。`BeanPostProcessor` 接口定义的两个方法，分别在 **bean的初始化方法执行的前后执行**（ `InitializingBean` 接口，或者 `init-method` 定义的方法）：

```java
package org.springframework.beans.factory.config;

import org.springframework.beans.BeansException;

/**
 * 用来自定义创建的 Bean，如：检查 Bean 的注解 或 用代理类进行包装 等
 *
 * postProcessBeforeInitialization 一般用来检查bean的注解 或者 填充 bean
 * postProcessAfterInitialization 一般用来代理类来包装 bean
 *
 * @see InstantiationAwareBeanPostProcessor
 * @see DestructionAwareBeanPostProcessor
 * @see ConfigurableBeanFactory#addBeanPostProcessor
 * @see BeanFactoryPostProcessor
 */
public interface BeanPostProcessor {

	/**
	 * 在bean的初始化方法执行 前 执行
	 *
	 * @see org.springframework.beans.factory.InitializingBean#afterPropertiesSet
	 */
	Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;

	/**
	 * 在bean的初始化方法执行 后 执行
	 *
	 * @see org.springframework.beans.factory.InitializingBean#afterPropertiesSet
	 * @see org.springframework.beans.factory.FactoryBean
	 */
	Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;

}
```

JSR注解 `@PostConstruct` 的处理就是使用`BeanPostProcessor`。它的另外一个较常见的使用场景是 spring-aop动态代理 

## @PostConstruct

默认情况下，Spring不会意识到`@PostConstruct`和 `@PreDestroy`注解。要启用它，要么注册`<bean class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor" />`，要么开启 Spring 的注解扫描功能 `<context:annotation-config />` 



## Read More

- [spring之扩展点](https://blog.csdn.net/windsunmoon/article/details/44283585)
- [源码解析：init-method、@PostConstruct、afterPropertiesSet孰先孰后](http://sexycoding.iteye.com/blog/1046993)