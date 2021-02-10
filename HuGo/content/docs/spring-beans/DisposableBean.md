# DisposableBean // TODO



定义在 spring 容器 销毁前 所做的操作方式 常见有三种：

1.  `@PreDestroy` 注解
2. 在 xml 中 bean 标签 配置  `destory-method` 属性 指定销毁方法
3. Bean 实现 `DisposableBean` 接口

