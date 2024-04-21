# Springmvc

<img src="D:\微信\WeChat Files\WeChat Files\wxid_ukvopicn0hju12\FileStorage\Temp\88d74945aa096a21f79cbe00757a3b1.jpg" alt="88d74945aa096a21f79cbe00757a3b1" style="zoom:150%;" />

1，在web.xml中（1），注册DispatcherServlet（前端控制器：用来接收方法路径，接收后查HanderMapping是否有相对应路径配置）

​							 （2），配置Ioc容器的配置文件

​							 （3），配置ServletMapping

```xml
<!--注册dispatcherservlet-->
    <servlet>
        <servlet-name>ds</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--        配置tomcat一启动就创建-->
        <load-on-startup>1</load-on-startup>
        
        <!--指定servlet配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        
    </servlet>

<!--    配置拦截的消息-->
    <servlet-mapping>
        <servlet-name>ds</servlet-name>
        <url-pattern>/</url-pattern>//拦截除json外的所有
    </servlet-mapping>
```

2，在spring.xml中，配置HanderMapping、HanderAdapter（总是需要），ViewResoiver（有前端交互需用到，可能不需要）

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"></bean>
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"></bean>
```

3,controller类

@RequestMapping注解

1，精准匹配

单层路径：/user

多层路径：/user/register

多个路径：{"/user/register","/user/reg"}

2，模糊匹配(精准匹配优先于模糊匹配)

模糊匹配单层路径：/user/*(/user/a)

模糊匹配多层路径：/user/**(例/user/a/ab/abc/abcd)

模糊匹配指定路径：/user/*/*

3，类和方法

当前controller类中，全部方法相同路径@RequestMapping

4，限制请求方法

@RequestMapping(value = {"/user/register","user/reg"},method = RequestMethod.PosT)

5，请求进阶

@GetMapping("/user/exit")

@PostMapping("/user/exit")

```java
@Controller
@RequestMapping("/user")
public class ControllerFirst {


    @RequestMapping("/hello")
    @ResponseBody
    public String hello()
    {
        return "this is first-hello";
    }

    @RequestMapping({"/user/register", "user/reg"})
    @ResponseBody
    public String register()
    {
        return "user register";
    }
    
    @PostMapping(/*/*/*)
    @GetMapping
    public String exit()
    {
        return "user exit";
    }
}
```

#### param参数接收

1，直接接收



2，@requestparam注解

（1）required默认为true，在该参数不传时会报400错误；

（2）defaul默认值

```java
@GetMapping("/first")
@ResponseBody
public String First(@RequestParam(value = "Name",required = false,defaultValue = "cxk") String name,@RequestParam("age") Integer age){
    System.out.println("name = " + name + ", age = " + age);
    return "first is ok";
}
```

3，一体多值

```java
@GetMapping("/second")
@ResponseBody
public String Second(@RequestParam("users") List<String> users){
    users.forEach(System.out::println);
    return "second is ok";
}
```

4，实体接收

```java
@GetMapping("/third")
@ResponseBody
public String Third(User user)
{
    System.out.println(user);
    return "third is ok";
}
```

#### 路径传参PathVariable



```java
@GetMapping("/{id}")
public String first(@PathVariable("id")Integer id)
{
    System.out.println(id);
    return "first id ok";
}

@GetMapping("/{name}/{age}")
public String second(@PathVariable("name") String name,@PathVariable("age") Integer age){
    System.out.println("name = " + name + ", age = " + age);
    return "second is ok";
}
```

#### 接收json数据

配置文件添加

```xml
xmlns:mvc="http://www.springframework.org/schema/mvc"
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
```

传入数据

{

  "name":"cyz",

  "age":18,

  "home":"jieyang"

}



实体类User**（需添加无参函数）**

```java
private String name;
private Integer age;
private String home;
```



1，导入jackson-databind依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
</dependency>
```

2，配置装配器

```java
<mvc:annotation-driven/>
```

3，使用@RequestBody

```java
@Controller
@RequestMapping("/json")
public class jsonController {

    @PostMapping("/first")
    @ResponseBody
    public String first(@RequestBody User user)
    {
        System.out.println(user);
        return "first is ok";
    }
}
```

#### 获取cookie

```java
    @GetMapping("/setCookie")
    @ResponseBody
    public String setcookie(HttpSession session)
    {
        System.out.println(session.getId());
        return "set is ok";
    }


    @GetMapping("/getCookie")
    @ResponseBody
    public String getcookie(@CookieValue("JSESSIONID") String sessionid)
    {
        System.out.println("sessionid = " + sessionid);
        return "get is ok";
    }
```





#### 获取请求头数据



```java
@Controller
@RequestMapping("header")
public class HeaderController {

    @GetMapping("/accpet")
    @ResponseBody
    public String getAccept(@RequestHeader("Accept") String accept)
    {
        System.out.println("accept = " + accept);
        return "getAccpet is ok";
    }
}
```



#### 跳转jsp界面

![image-20240401233038879](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240401233038879.png)

在webapp配置一个index.jsp

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${requestScope.msg}
</body>
</html>
```



类

```java
@Controller
@RequestMapping("/jsp")
public class jspController {

    @GetMapping("/index")
    public String toIndex(Model model){
        model.addAttribute("msg","今晚夜色真美");
        return "index";
    }
}
```



springmvc.xml

```xml
<context:component-scan base-package="com.java"></context:component-scan>

<mvc:annotation-driven></mvc:annotation-driven>

<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">//view视图
    <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"></property>
    <property name="prefix" value="/WEB-INF/views/"></property>//前缀
    <property name="suffix" value=".jsp"></property>//后缀
</bean>
```

##### 转发

```java
@GetMapping("/jump")
public String jump(Model model)
{
    model.addAttribute("msg","转发一下");
    return "forward:/jsp/forward";
}

//转发
@GetMapping("/forward")
public String forward()
{
    return "index";
}
```

##### 重定向

```java
@GetMapping("/rejump")
public String rejump(Model model)
{
    model.addAttribute("msg","重定向一下");
    return "redirect:/jsp/redirect";
}
@GetMapping("redirect")
public String redirect()
{
    System.out.println("haha");
    return "index";
}
```

#### 返回json数据

```java
@Controller
@RequestMapping("/Json")
public class JsonController {

    @GetMapping("/getjson")
    @ResponseBody
    //1，如果返回值为String等，则转为字符串类型
    //2，如果返回值为对象、引用类型，则转为json类型
    public User user(@RequestBody User user)
    {
        System.out.println("user = " + user);//接收json数据
        User user1=new User("kk","女","05");
        return user1;//返回json数据
    }
}
```

#### 访问静态数据

```xml
<mvc:default-servlet-handler></mvc:default-servlet-handler>
```

配置文件装配Defaultservlet，然后直接url访问

#### RESTful开发风格

增删改查

增:POST

删:DELECT

改:PUT

查:GET



```JAVA
package com.java.Controller;

import com.java.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/user")
public class UserController {


    @GetMapping("/{id}")
    public String selectByget(@PathVariable("id") Integer id)
    {
        System.out.println("id = " + id);
        return "select is ok";
    }


    @PostMapping
    public String addBypost(@RequestBody User user)
    {
        System.out.println("user = " + user);
        return "add is ok";
    }


    @DeleteMapping("/{id}")
    public String delBydelect(@PathVariable Integer id)
    {
        System.out.println("id = " + id);
        return "delect is ok";
    }


    @PutMapping()
    public String midByput(@RequestBody User user)
    {
        System.out.println("user = " + user);
        return "mid is ok";
    }
}
```

#### 异常处理



创建一个异常类

```java
package com.java.exception;


import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

@ControllerAdvice
public class ControllerExceptionHandler {

    @ExceptionHandler(ArithmeticException.class)
    @ResponseBody
    public String handlerArithmeticException(ArithmeticException exception)
    {
        System.out.println(exception.getMessage());
        return "ari is ok";
    }

    @ExceptionHandler(ClassCastException.class)//类型转换 数字转换出现异常，例转字符串
    @ResponseBody
    public String handlerClassCastException(ClassCastException classCastException)
    {
        System.out.println(classCastException.getMessage());
        return "class is ok";
    }

    @ExceptionHandler(NullPointerException.class)//是当您尝试使用指向内存中空位置的引用（null）时发生的异常
    @ResponseBody
    public String handlerNullPointerException(NullPointerException nullPointerException)
    {
        System.out.println(nullPointerException.getMessage());
        return "null is ok";
    }

    @ExceptionHandler(NumberFormatException.class)//类型转换 字符串转数字出现异常
    @ResponseBody
    public String handlerNumberFormatException(NumberFormatException NumberFormatException)
    {
        System.out.println(NumberFormatException.getMessage());
        return "num is ok";
    }

    @ExceptionHandler(Exception.class)//异常处理，可能出现无法预测的异常
    @ResponseBody
    public String handlerException(Exception Exception)
    {
        System.out.println(Exception.getMessage());
        return "num is ok";
    }
}
```

#### 拦截器

1,配置拦截器类

继承HandlerInterception方法，实现preHandle（执行前）、postHandle（执行后）、afterCompletion（调用后）方法，

```java
public class ControllerInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("ControllerInterceptor的preHandle方法执行了");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("ControllerInterceptor的postHandle方法执行了");

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("ControllerInterceptor的afterCompletion方法执行了");

    }
}
```

2,配置xml配置文件

a拦截u类，b拦截u类，都放行则:

a的preHandle-b的preHandle-执行目标方法-b的postHandle-a的postHandle-b的afterCompletion-a的afterCompletion

a拦截u类，b拦截u类，a不放行则:

a的preHandle-b的preHandle-a的afterCompletion

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/First/*"/>
        <mvc:exclude-mapping path="/First/res"/>
        <bean class="com.java.Interceptor.ControllerInterceptor">
        </bean>
    </mvc:interceptor>
</mvc:interceptors>
```

#### 参数校验的注解

![cdab72b04b33e4667968c38d08c61de](D:\微信\WeChat Files\WeChat Files\wxid_ukvopicn0hju12\FileStorage\Temp\cdab72b04b33e4667968c38d08c61de.png)

@Null参数必须为空

@AssrtTrue参数必须为真

@Min()最小值为

@DecimalMax()最大值为，小数

@Size()字符串或数组、字典等长度

@Digits()整数几位数，小数几位数

@Past,@Future 用于时间

@Pattern邮箱正则表达式

#### 文件上传/下载



```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>
```



```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver"></bean>
```



```xml
<!--注册dispatcherservlet-->
<servlet>
    <servlet-name>ds</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--        配置tomcat一启动就创建-->


    <!--指定servlet配置文件-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
    <multipart-config>
        <!--            单个文件上传的-->
        <max-file-size>10485760</max-file-size>
        <!--            单次上传-->
        <max-request-size>20971520</max-request-size>
        <!--            定义文件内存的最大值，超过最大值会被写入磁盘-->
        <file-size-threshold>5242880</file-size-threshold>
    </multipart-config>
</servlet>

<!--    配置拦截的消息-->
<servlet-mapping>
    <servlet-name>ds</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

##### 文件上传

```html
<form method="Post" action="file/upload" enctype="multipart/form-data">
    名字<input type="text" name="name">
    图片<input type="file" name="picture">
    <button type="submit">提交</button>
</form>
```



```java
@Autowired
private ServletContext servletContext;

@ResponseBody
@PostMapping("/upload")
public String fileUpload(@RequestParam(value = "name") String name,@RequestParam(value = "picture") MultipartFile picture) throws IOException {
    System.out.println("name="+name);

    String realPath=servletContext.getRealPath("/images");
    System.out.println(realPath);

    String uuid1= UUID.randomUUID().toString().replaceAll("-","");
    String newFileName=uuid1+"_"+picture.getOriginalFilename();

    picture.transferTo(new File(realPath+File.separator+newFileName));
    return "ok";
}
```



##### 文件下载



```html
<a href="file/down?filename=3186d6fe564146178a2062f4f8ab169d_柴犬2.jpg">3186d6fe564146178a2062f4f8ab169d_柴犬2.jpg</a>
```



```java
@GetMapping("/down")
public ResponseEntity<byte[]> downFile(@RequestParam("filename") String filename) throws IOException {
    String realPath = servletContext.getRealPath("/images");

    String pathName= realPath + File.separator +filename;

    FileInputStream fileInputStream=new FileInputStream(pathName);
    byte[] bytes=new byte[fileInputStream.available()];
    fileInputStream.read(bytes);

    HttpHeaders httpHeaders=new HttpHeaders();
    httpHeaders.add("content-disposition","attachment;filename"+URLEncoder.encode(filename,"UTF-8"));
    ResponseEntity<byte[]> responseEntity=new ResponseEntity<>(bytes,httpHeaders, HttpStatus.OK);
    return responseEntity;
```

 
