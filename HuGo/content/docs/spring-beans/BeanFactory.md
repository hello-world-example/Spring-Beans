# BeanFactory

`BeanFactory` 是个 **工厂**，也就是 `IOC 容器` 或 `对象工厂` ，所有的 `Bean` 都是由 `BeanFactory`( 也就是 IOC 容器 ) 来进行管理。

BeanFactory定义了 IOC 容器的最基本形式，并提供了 IOC 容器应遵守的的最基本的接口，也就是 Spring IOC 所遵守的最底层和最基本的编程规范。

```java
public interface BeanFactory {
    // FactoryBean前缀
    String FACTORY_BEAN_PREFIX = "&";

    // 根据名称获取Bean对象
    Object getBean(String name) throws BeansException;

    // 根据名称、类型获取Bean对象
    <T> T getBean(String name, Class<T> requiredType) throws BeansException;

    // 根据类型获取Bean对象
    <T> T getBean(Class<T> requiredType) throws BeansException;

    // 根据名称获取Bean对象,带参数
    Object getBean(String name, Object... args) throws BeansException;

    //根据类型获取Bean对象,带参数
    <T> T getBean(Class<T> requiredType, Object... args) throws BeansException;

    // 是否存在
    boolean containsBean(String name);

    // 是否为单例
    boolean isSingleton(String name) throws NoSuchBeanDefinitionException;

    // 是否为原型（多实例）
    boolean isPrototype(String name) throws NoSuchBeanDefinitionException;

    // 名称、类型是否匹配
    boolean isTypeMatch(String name, Class<?> targetType) throws NoSuchBeanDefinitionException;

    /**
     * 
     * @since 4.2
     */
    boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;

    
    // 获取类型
    Class<?> getType(String name) throws NoSuchBeanDefinitionException;

    // 根据实例的名字获取实例的别名
    String[] getAliases(String name);
}
```



## Read More

- [Spring 中 FactoryBean 与 BeanFactory 的区别](https://www.cnblogs.com/ninth/p/6405366.html)