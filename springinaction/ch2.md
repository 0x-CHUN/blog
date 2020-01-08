# 装配Bean

## Spring 配置的可选方案

主要有三种：

1. 在 XML 中进行显式配置。
2. 在 Java 中进行显式配置。
3. 隐式的 bean 发现机制和自动装配。

推荐采用第三种，并尽可能的减少显式配置，显式配置也尽可能的采用Java config；

## 自动化装配 bean

spring从两个角度来实现自动化装配：

- 组件扫描（component scanning）：Spring 会自动发现应用上下文中所创建的 bean。
- 自动装配（autowiring）：Spring 自动满足 bean 之间的依赖。

### 创建可被发现的 bean

可以使用@Bean，推荐使用@Component，spring会为其自动配置，然后从再用@ComponentScan 注解启用了组件扫描。

测试时用Spring 的 SpringJUnit4ClassRunner，以便在测试开始的时候自动创建 Spring 的应用上下文。注解 @ContextConfiguration 会告诉需要在哪个Java类中加载配置。

 @Autowired则会在组件扫描出的组件中获取到对象。

### 为组件扫描的 bean 命名

两种方式：

1. @Component("name")
2. @Named("name")（较少用）

### 设置组件扫描的基础包

1. @ComponentScan("包名")
2. @ComponentScan(basePackages="包名")
3. @ComponentScan(basePackages={"包名1", "包名2"})
4. @ComponentScan(basePackageClasses={类名1 ，类名2})

### 通过为 bean 添加注解实现自动装配

为了声明要进行自动装配，可以借助 Spring 的 @Autowired 注解。