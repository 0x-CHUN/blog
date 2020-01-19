# Spring MVC 的高级技术

## Spring MVC 配置的替代方案

### 自定义 DispatcherServlet 配置

之前配置的三个方法是必须要重载的三个abstract方法，实际上还有更多的方法可以进行重载，从而实现额外配置。

实现的方法就是：**customizeRegistration()**

在 AbstractAnnotationConfig-DispatcherServletInitializer 将 DispatcherServlet 注册到 Servlet 容器中之后，就会调用 customizeRegistration()，并将 Servlet 注册后得到的 Registration.Dynamic 传递进来。通过重载 customizeRegistration() 方法，我们可以对 Dispatcher-Servlet 进行额外的配置。

### 添加其他的 Servlet 和 Filter

基于 Java 的初始化器（initializer）的一个好处就在于我们可以定义任意数量的初始化器类。因此，如果我们想往 Web 容器中注册其他组件的话，只需创建一个新的初始化器就可以了。最简单的方式就是实现 Spring 的 WebApplicationInitializer 接口。

创建 WebApplicationInitializer 实现并注册一个 Servlet:

```java
package com.myapp.config;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRegistration.Dynamic;
import org.springframework.web.WebApplicationInitializer;
import com.myapp.MyServlet;

public class MyServletInitializer extends WebApplicationInitializer {
  
  @Override
  public void onStartup(ServletContext servletContext) throws ServletException {
    Dynamic myServlet = servlectContext.addServlet("myServlet", MyServlet.class);
    
    myServlect.addMapping("/custom/**");
  }

}
```

类似地，还可以创建新的 WebApplicationInitializer 实现来注册 Listener 和 Filter。

## 处理 multipart 形式的数据

### 配置 multipart 解析器

从 Spring 3.1 开 始，Spring 内置了两个 MultipartResolver 的实现供我们选择：

- CommonsMultipartResolver：使用 Jakarta Commons FileUpload 解析 multipart 请求；
- StandardServletMultipartResolver：依赖于 Servlet 3.0 对 multipart 请求的支持（始于 Spring 3.1）。

兼容 Servlet 3.0 的 StandardServletMultipartResolver 没有构造器参数，也没有要设置的属性。这样，在 Spring 应用上下文中，将其声明为 bean 就会非常简单，如下所示：

```java
@Bean
public MultipartResolver multipartResolver() throws IOException {
  return new StandardServletMultipartResolver();
}
```

可以通过重载customize-Registration() 方法（它会得到一个 Dynamic 作为参数）来配置 multipart 的具体细节：

```java
@Override
protected void customizeRegistration(Dynamic registration) {
  registration.setMultipartConfig(
    new MultipartConfigElement("/tmp/spittr/uploads",
      2097152, 4194304, 0);
  ); //限制文件的大小不超过 2MB，整个请求不超过 4MB，而且所有的文件都要写到磁盘中。
}
```

### 处理 multipart 请求

编写控制器方法来接收上传的文件。要实现这一点，最常见的方式就是在某个控制器方法参数上添加 @RequestPart 注解。

```java
@RequestMapping(value="/register", method=POST)
public String processRegistration(
    @RequestPart("profilePicture") byte[]  profilePicture,
    @Valid Spittr spittr,
    Errors errors) {
  ...
} 
```

Spring 还提供了 MultipartFile 接口，它为处理 multipart 数据提供了内容更为丰富的对象。

```java
package org.springframework.web.multipart

import java.io.File;
import java.io.IOException;
import java.io.InputStream;

public interface MultipartFile {
  String getName();
  String getOriginalFilename();
  String getContentType();
  boolean isEmpty();
  long getSize();
  byte[] getBytes() throws IOException;
  InputStream getInputStream() throws IOException;
  void transferTo(File dest) throws IOException;
}
```

除此之外，MultipartFile 还提供了一个便利的 transferTo() 方 法，它能够帮助我们将上传的文件写入到文件系统中。

```java
profilePicture.tranferTo(new File("/data/spittr/" + profilePicture.getOriginalFilename()));
```

## 处理异常

Spring 提供了多种方式将异常转换为响应：

- 特定的 Spring 异常将会自动映射为指定的 HTTP 状态码；
- 异常上可以添加 @ResponseStatus 注解，从而将其映射为某一个 HTTP 状态码；
- 在方法上可以添加 @ExceptionHandler 注解，使其用来处理异常。

### 将异常映射为 HTTP 状态码

异常上可以添加 @ResponseStatus 注解，从而将其映射为某一个 HTTP 状态码；

```java
@RequestMapping(value="/{spittleId}", method=RequestMethod.GET)
public String spittle(
    @PathVariable("spittleId") long spittleId, 
    Model model) {
  Spittle spittle = spittleRepository.findOne(spittleId);
  if (spittle == null) {
    throw new SpittleNotFoundException();
  }
  model.addAttribute(spittle);
  return "spittle";
}
```

上述请求会产生404状态，使用 @ResponseStatus 注解将 SpittleNotFoundException 映射为 HTTP 状态码 404：

```java
package spittr.web;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(value=HttpStatus.NOT_FOUND, reason="Spittle Not Found")
public class SpittleNotFoundException extends RuntimeException {
}
```

在引入 @ResponseStatus 注解之后，如果控制器方法抛出 SpittleNotFound-Exception 异常的话，响应将会具有 404 状态码，这是因为 Spittle Not Found。

### 编写异常处理的方法

```java
@ExceptionHandler(DuplicateSpittleException.class)
public String handleDuplicateSpittle() {
  return "error/duplicate";
}
```

**handleDuplicateSpittle()** 方法上添加了 @ExceptionHandler 注解，当抛出  DuplicateSpittleException 异常的时候，将会委托该方法来处理。

@ExceptionHandler 注解所标注的方法能够处理同一个控制器类中所有处理器方法的异常。

通过将其定义到控制器通知类中使其能够处理所有控制器中处理器方法所抛出的异常。

## 为控制器添加通知

控制器通知（controller advice）是任意带有 @ControllerAdvice 注解的类，这个类会包含一个或多个如下类型的方法：

- @ExceptionHandler 注解标注的方法；
- @InitBinder 注解标注的方法；
- @ModelAttribute 注解标注的方法。

在带有 @ControllerAdvice 注解的类中，以上所述的这些方法会运用到整个应用程序所有控制器中带有 @RequestMapping 注解的方法上。

@ControllerAdvice 注解本身已经使用了 @Component，因此@Controller-Advice 注解所标注的类将会自动被组件扫描获取到，就像带有 @Component 注解的类一样。

@ControllerAdvice 最为实用的一个场景就是将所有的 @ExceptionHandler 方法收集到一个类中，这样所有控制器的异常就能在一个地方进行一致的处理。

```java
package spittr.web;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class AppWideExceptionHandler {

  @ExceptionHandler(DuplicateSpittleException.class)
  public String handleNotFound() {
    return "error/duplicate";
  }

}
```

现在，如果任意的控制器方法抛出了 DuplicateSpittleException，不管这个方法位于哪个控制器中，都会调用这个 duplicateSpittleHandler() 方法来处理异常。