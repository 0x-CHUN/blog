# 使用 Spring Web Flow

## 在 Spring 中配置 Web Flow

Web Flow只支持XML配置；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:flow="http://www.springframework.org/schema/webflow-config"
 xsi:schemaLocation="http://www.springframework.org/schema/webflow-config 
   http://www.springframework.org/schema/webflow-config/spring-webflow-config-2.3.xsd
   http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
​
</beans>
```

### 装配流程执行器

流程执行器（flow executor）驱动流程的执行，当用户进入一个流程时，流程执行器会为用户创建并启动一个流程执行实例。当流程暂停的时候（如为用户展示视图时），流程执行器会在用户执行操作后恢复流程。

创建一个流程执行器：

```xml
<flow:flow-executor id="flowExecutor" />
```

流程执行器负责创建和执行流程，而流程注册表（flow registry）负责加载流程定义；

### 配置流程注册表

流程注册表（flow registry）的工作是加载流程定义并让流程执行器能够使用它们。

```xml
<flow:flow-registry id="flowRegistry" base-path="/WEB-INF/flows">
  <flow:flow-location-pattern value="*-flow.xml" />
</flow:flow-registry>
```

在这里的声明中，流程注册表会在 `/WEB-INF/flows` 目录下查找流程定义，这是通过 base-path 属性指明的。依据 `<flow:flow-location-pattern>` 元素的值，任何文件名以 `-flow.xml` 结尾的 XML 文件都将视为流程定义。

### 处理流程请求

对于流程而言，需要一个 FlowHandlerMapping 来帮助 DispatcherServlet 将流程请求发送给 Spring Web Flow。在 Spring 应用上下文中，FlowHandlerMapping 的配置如下：

```xml
<bean class="org.springframework.webflow.mvc.servlet.FlowHandlerMapping">
  <property name="flowRegistry" ref="flowRegistry" />
</bean>
```

FlowHandlerMapping 装配了流程注册表的引用，这样它就能知道如何将请求的 URL 匹配到流程上。

FlowHandlerMapping 的工作仅仅是将流程请求定向到 Spring Web Flow 上，响应请求的是 FlowHandlerAdapter。FlowHandlerAdapter 等同于 Spring MVC 的控制器，它会响应发送的流程请求并对其进行处理。FlowHandler-Adapter 可以像下面这样装配成一个 Spring bean:

```xml
<bean class="org.springframework.webflow.mvc.servlet.FlowHandlerAdapter">
  <property name="flowExecutor" ref="flowExecutor" />
</bean>
```

这个处理适配器是 DispatcherServlet 和 Spring Web Flow 之间的桥梁。它会处理流程请求并管理基于这些请求的流程。

## 流程的组件

### 状态

Spring Web Flow 定义了五种不同类型的状态:

1. 行为（Action）: 行为状态是流程逻辑发生的地方
2. 决策 （Decision）: 决策状态将流程分成两个方向，它会基于流程数据的评估结果确定流程方向
3. 结束（End）: 结束状态是流程的最后一站。一旦进入End状态，流程就会终止
4. 子流程 （Subflow）: 子流程状态会在当前正在运行的流程上下文中启动一个新的流程
5. 视图（View）: 视图状态会暂停流程并邀请用户参与流程

**视图状态**

视图状态用于为用户展现信息并使用户在流程中发挥作用。实际的视图实现可以是 Spring 支持的任意视图类型，但通常是用 JSP 来实现的。在流程定义的 XML 文件中，`<view-state>` 用于定义视图状态：

```xml
<view-state id="welcome" />
```

**行为状态**

视图状态会涉及到流程应用程序的用户，而行为状态则是应用程序自身在执行任务。行为状态一般会触发 Spring 所管理 bean 的一些方法并根据方法调用的执行结果转移到另一个状态。

在流程定义 XML 中，行为状态使用 `<action-state>` 元素来声明。这里是一个例子：

```xml
<action-state id="saveOrder">
  <evaluate expression="pizzaFlowActions.saveOrder(order)" />
  <transition to="thankYou" />
</action-state>
```

**决策状态**

有可能流程会完全按照线性执行，从一个状态进入另一个状态，没有其他的替代路线。但是更常见的情况是流程在某一个点根据流程的当前情况进入不同的分支。

决策状态能够在流程执行时产生两个分支。决策状态将评估一个 Boolean 类型的表达式，然后在两个状态转移中选择一个，这要取决于表达式会计算出 true 还是 false。在 XML 流程定义中，决策状态通过 `<decision-state>` 元素进行定义。典型的决策状态示例如下所示：

```xml
<decision-state id="checkDeliveryArea">
  <id test="pizzaFlowActions.checkDeliveryArea(customer.zipCode)"
      then="addCustomer"
      else="deliveryWarning" />
</decision-state>
```

**子流程状态**

你可能不会将应用程序的所有逻辑写在一个方法中，而是将其分散到多个类、方法以及其他结构中。

同样，将流程分成独立的部分是个不错的主意。允许在一个正在执行的流程中调用另一个流程。这类似于在一个方法中调用另一个方法。

`<suflow-state>` 可以这样声明:

```xml
<subflow-state id="order" subflow="pizza/order">
  <input name="order" value="order" />
  <transition on="orderCreated" to="payment" />
</subflow-state>
```

**结束状态**

最后，所有的流程都要结束。这就是当流程转移到结束状态时所做的。`` 元素指定了流程的结束，它一般会是这样声明的：

```xml
<end-state id="customerReady" />
```

### 转移

转移使用 `<transition>` 元素来进行定义，它会作为各种状态元素（`<action-state>`、`<view-state>` 、`<subflow-state>`）的子元素。

on 属性来指定触发转移的事件,属性 to 用于指定流程的下一个状态。

### 流程数据

**声明变量**

流程数据保存在变量中，而变量可以在流程的各个地方进行引用。它能够以多种方式创建。在流程中创建变量的最简单形式是使用 `<var>` 元素：

**定义流程数据的作用域**

流程中携带的数据会拥有不同的生命作用域和可见性，这取决于保存数据的变量本身的作用域。Spring Web Flow 定义了五种不同作用域

* Conversation    : 最高层级的流程开始时创建，在最高层级的流程结束时销毁。被最高层级的流程和其所有的子流程所共享
* Flow : 当流程开始时创建，在流程结束时销毁。只有在创建它的流程中是可见的
* Request : 当一个请求进入流程时创建，在流程返回时销毁
* Flash : 当流程开始时创建，在流程结束时销毁。在视图状态渲染后，它也会被清除
* View : 当进入视图状态时创建，当这个状态退出时销毁。只在视图状态内是可见的