# Spring boot基础

## MVC

Model、View、Controller 三者关系：

​        ![img](assets/6unsBfGJUD4zF3HF__thumbnail-1578380652061)      

## 注解

demo 的注解**@SpringBootApplication** 是一个复合注解，添加了一下注解：

* @Configuration：将当前类以@Bean 的注解方法标记到容器的上下文
* @EnableAutoConfiguration：启动自动配置
* @ComponentScan：扫描当前包及其子包下被@controller、@component、@service 注解的类，并添加到容器内进行管理。

开发里常用的注解：

* @controller：定义控制器类，控制将用户发来的用户请求，转发到相应的服务接口。

  ```java
  @Controller
  public class HelloController {
      private Map<String, Object> result = new HashMap<>();
      @RequestMapping("/hello")
      @ResponseBody
      public Map<String, Object> hello() {
          result.put("name", "Stephen");
          result.put("city", "San Jose");
          return result;
      }
  }
  
  ```

  一般搭配@ResponseBody 使用，@ResponseBody 是用来改变返回值的类型，不添加@ResponseBody 返回的是跳转路径，添加后返回的写入 HTTP response Body 的 json 数据。

* @Restcontroller：结合@ResponseBody 和@Controller 的注解，一般用于构建 restful api。

## Servlet

servlet 是服务端的小程序，用来处理 web 应用中的请求。

```java
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name="helloServlet", urlPatterns ="/helloServlet")
public class HelloServlet extends HttpServlet{
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("Running Hello Servlet doGet method");
    }
}

```

@WebServlet 创建一个 servlet 类，在@SpringBootApplication 下添加@ServletComponentScan 使 spring 可以扫描到自定义的 servlet 类。



## Filter

Filter 是一个组件用来预处理或者后加工用户请求的类。

在上面的 servlet 类下添加

```java 
@WebFilter(filterName="helloFilter", urlPatterns="/helloServlet")
public class HelloFilter implements Filter { 
    @Override
    public void doFilter(ServletRequest servletRequest,ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("Executing doFilter method");
        filterChain.doFilter(servletRequest, servletResponse);
        System.out.println("Done executing doFilter method");
    }
}
// 执行结果
// Executing doFilter method
// Running Hello Servlet doGet method
// Done executing doFilter method
```

Filter 流程：

1. 解析 doFliter 方法
2. 执行 servlet 方法
3. 完成执行 doFilter 方法

## Listener

servlet 的监听器，会监听 servlet 的事件，如 ServletContextListener、ServletContextAttributeListener 等

```java 
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;
@WebListener
public class HelloListener implements ServletContextListener {
    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("Servlet Context Destroyed");
    }
    @Overrid
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("Servlet Context Initialized");
    }
}
```

## Bean

ava bean 是一种规范，表达实体和信息的规范，便于复用。参考 [链接](https://www.zhihu.com/question/19773379/answer/18307751) 

一般规范：

* 提供默认构造器，如无参构造器
* 所有属性为 private
* 提供 getter、setter 接口
* 实现 serializable 接口

```java
/// Register Servlet.
@Bean
public ServletRegistrationBean getServletRegistrationBean() {
  ServletRegistrationBean servletBean = new ServletRegistrationBean(new HelloServlet());
  servletBean.addUrlMappings("/helloServlet");
  return servletBean;
}
/// Register Filter.
@Bean
public FilterRegistrationBean getFilterRegistrationBean() {
  FilterRegistrationBean filterBean = new FilterRegistrationBean(new HelloFilter());
// Add filter path
  filterBean.addUrlPatterns("/helloServlet");
  return filterBean;
}
@Bean
public ServletListenerRegistrationBean<HelloListener> getServletListenerRegistrationBean() {
  ServletListenerRegistrationBean listenerBean =new ServletListenerRegistrationBean(new HelloListener());
  return listenerBean;
}
```

