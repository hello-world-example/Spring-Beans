# FactoryBean

`FactoryBean` 是一个 `Bean`，但不是一个简单的`Bean`，是一个能生产对象的 `Bean`   😓  

接口如下：

``` java
package org.springframework.beans.factory;

public interface FactoryBean<T> {

    // FactoryBean 创建的 Bean 实例
    T getObject() throws Exception;

    // 返回 FactoryBean 创建的 Bean 类型
    Class<?> getObjectType();

    // 返回由 FactoryBean 创建的 Bean 实例的作用域是 singleton 还是 prototype
    boolean isSingleton();
}
```



## 使用示例

### User.java

```java
package xyz.kail.blog.spring;

public class User {
    public String name;
}
```

### UserFactoryBean.java

```java
package xyz.kail.blog.spring;

import org.springframework.beans.factory.FactoryBean;
import org.springframework.stereotype.Component;

@Component("user")
public class UserFactoryBean implements FactoryBean<User> {

    @Override
    public User getObject() throws Exception {
        User user = new User();
        user.name = "kail";
        System.out.println("创建 User 成功");
        return user;
    }

    @Override
    public Class<?> getObjectType() {
        return User.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}

```

### Main.java 测试

```java
package xyz.kail.blog.spring;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {

    public static void main(String[] args) {

        BeanFactory context = new AnnotationConfigApplicationContext("xyz.kail.blog");

        // xyz.kail.blog.spring.User@xxxxxxxx1
        System.out.println(context.getBean(User.class));
        // xyz.kail.blog.spring.UserFactoryBean@xxxxxxxx2
        System.out.println(context.getBean(UserFactoryBean.class));

        // xyz.kail.blog.spring.User@xxxxxxxx1
        System.out.println(context.getBean("user", User.class));
        // 使用 &前缀("&user") 可以获取FactoryBean本身
        // xyz.kail.blog.spring.UserFactoryBean@xxxxxxxx2
        System.out.println(context.getBean(BeanFactory.FACTORY_BEAN_PREFIX + "user", UserFactoryBean.class));

    }

}

```



## SmartFactoryBean

通过 SmartFactoryBean 可以创建 懒加载的 `Bean`

接口如下

```java
package org.springframework.beans.factory;

public interface SmartFactoryBean<T> extends FactoryBean<T> {

	// 创建的 Bean 实例的作用域是 prototype 还是 singleton
	boolean isPrototype();

	/**
	 * 是否急于初始化
	 * true 急: 容器初始化的完成的时候 就创建该 Bean
	 * false 不急: 用到的时候再创建 
	 */
	boolean isEagerInit();

}

```

### 使用示例

#### UserLazyFactoryBean.java

```java
package xyz.kail.blog.spring;

import org.springframework.beans.factory.SmartFactoryBean;
import org.springframework.stereotype.Component;

@Component("lazy-user")
public class UserLazyFactoryBean implements SmartFactoryBean<User> {

    public static Boolean lazy = false;

    ... 同 UserFactoryBean.java

    @Override
    public boolean isPrototype() {
        return !isSingleton();
    }

    @Override
    public boolean isEagerInit() {
        return !lazy;
    }
}

```



#### Main.java

```java
package xyz.kail.blog.spring;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {

    public static void main(String[] args) {

        /*
         * 先输出： context success
         * 再输出： 创建 User 成功
         */
        UserLazyFactoryBean.lazy = true;
        BeanFactory contextLazyTest = new AnnotationConfigApplicationContext("xyz.kail.blog");
        System.out.println("context success");
        contextLazyTest.getBean("lazy-user", User.class);



        /*
         * 先输出： 创建 User 成功
         * 再输出： context success
         */
        UserLazyFactoryBean.lazy = false;
        BeanFactory contextNotLazyTest = new AnnotationConfigApplicationContext("xyz.kail.blog");
        System.out.println("context success");
        contextNotLazyTest.getBean("lazy-user", User.class);

    }
}
```





## Read More

- [Spring 中 FactoryBean 与 BeanFactory 的区别](https://www.cnblogs.com/ninth/p/6405366.html)



