# Spring

#### 快速入门

#### 快捷键

ctrl+ins 快速构造函数

alt+ins 快速构造setter，getter等等

ctrl+点击 查看类的方法

alt+回车 导入类，完善方法

#### 1，导入依赖

```java
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.1.3</version>
</dependency>
```

#### 2，创建类

```java
public class Demo1 {
    public void springtest1(){
        System.out.print("this is demo1");
    }

    public Demo1() {
        System.out.println("this is 构造方法");
    }//调用默认

}
```

#### 3，创建xml配置文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="demo1" class="com.spring.demo.Demo1"></bean> //id为唯一标识，class全路径
</beans>
```

#### 4，test类（路径与类相同）

```java
public class test {
    @Test
    public void test()
    {
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("Spring1.xml");
        Demo1 bean =(Demo1) applicationContext.getBean("demo1");
        bean.springtest1();
    }
}
```

#### 5，getbean三种方法 //id；class；id、class

```java
//第一种，id获取，得强转
Demo1 bean =(Demo1) applicationContext.getBean("demo1");

//第二种，class获取，无需强转，但有时候类型多
Demo1 bean = applicationContext.getBean(Demo1.class);

//第三种，id和class组合
Demo1 bean = applicationContext.getBean("demo1", Demo1.class);
```

#### 6，setter注入属性值 //需在类中有setter方法，可给属性赋值

##### 普通类型赋值

```java
<bean id="Demo1Bean1" class="com.spring.demo.Demo1">
    <property name="Demo1Data" value="我是赋值1"></property>
</bean>
```

##### List，set，map赋值

List

```java
<property name="classList">
    <list>
        <value>高一一班</value>
        <value>高一二班</value>
    </list>
</property>
```

set

```java
<property name="classSet">
    <set>
        <value>高二一班</value>
        <value>高二二班</value>
    </set>
</property>
```

map

```java
<property name="maps">
    <map>
        <entry key="高三" value="高三一班"></entry>
        <entry key="高四" value="高四一班"></entry>
    </map>
</property>
```



#### 7，引用其他bean赋值  //ref引用其他bean

```java
<bean id="Demo2Bean1" class="com.spring.demo.Demo2">
        <property name="Demo2Data" value="我是赋值2"></property>
</bean>

<bean id="Demo1Bean1" class="com.spring.demo.Demo1">
    <property name="Demo1Data" value="我是赋值1"></property>
    <property name="Demo2Data2" ref="Demo2Bean1"></property>
</bean>
```

#### 8，内部bean //内部引用不会改变其值

```java
<bean id="Demo2Bean1" class="com.spring.demo.Demo2">
        <property name="Demo2Data" value="我是赋值2"></property>
</bean>
    
<bean id="Demo1Bean2" class="com.spring.demo.Demo1">
    <property name="Demo1Data" value="我是赋值1"></property>
    <property name="Demo2Data2">
        <bean class="com.spring.demo.Demo2">
            <property name="Demo2Data" value="我是内部bean的赋值"></property>
        </bean>
    </property>
</bean>
```

#### 9，引入外部属性配置文件//一些类不是自己创建，但想要用ioc容器，例如druid



配置文件

//引入外部属性配置文件



```java
<context:property-placeholder location="classpath:db.properties"></context:property-placeholder>
```

```java
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
```

资源包//db

```java
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/demo
jdbc.username=root
jdbc.password=root
```

必要时在顶部添加

```java
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
```

且修改其中的context

```java
xmlns:context="http://www.springframework.org/schema/context
```

test类

```java
DruidDataSource druidDataSource= applicationContext.getBean("dataSource", DruidDataSource.class);
DruidPooledConnection connection = druidDataSource.getConnection();
System.out.println(connection);
```

#### 10，beans的作用域

默认ioc容器为单例，即无论用多少个对象获取bean，都使用同一个地址

且在单例情况下，即使无获取bean也会调用方法

scope标签可使为多例

```java
<bean id="class" class="com.java.Spring.Class" scope="prototype">
```

#### 11，beans的生命周期

输出顺序：构造函数-set方法-init初始化函数-输出结果-destroy销毁函数

输出顺序：实例化-依赖注入-初始化-方法-销毁函数

```java
<bean id="class" class="com.java.Spring.Class" init-method="init" destroy-method="destroy">
 //配置文件声明
```

##### init



##### 初始化的后置处理器

实现BeanPostProcessor方法并在xml中声明

```java
public class MyBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("初始化前的后置处理器");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("初始化后的后置处理器");
        return bean;
    }
}
```

##### destroy

```java
public void init()
{
    System.out.println("这是初始化函数");
}
public void destroy()
{
    System.out.println("这是销毁函数");
}//
```

ApplicationContext无法close，需使用ConfigurableApplicationContext

```java
ConfigurableApplicationContext applicationContext= new ClassPathXmlApplicationContext("Class.xml");
Class bean = applicationContext.getBean("class",Class.class);
System.out.println(bean);
applicationContext.close();
```

#### FactoryBean

实现一个FactoryBean类 继承接口

**配置完factorybean之后，也直接用Haha，无需配置Hahabean**

```java
public class HahaFactoryBean implements FactoryBean<Haha> {
    @Override
    public boolean isSingleton() {
        return true;
    }

    @Override
    public Haha getObject() throws Exception {
        return new Haha();
    }

    @Override
    public Class<?> getObjectType() {
        return Haha.class;
    }
}
```

```java
<bean class="com.java.Factory.HahaFactoryBean"></bean> //无需配置id
```

```java
public class Hahatest {
    @Test
    public void test()
    {
        ApplicationContext ioc = new ClassPathXmlApplicationContext("class.xml");
        Haha bean = ioc.getBean(Haha.class);
        bean.kk();
    }
}
```

### 基于xml实现三层

文件结构

![image-20240319124806937](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240319124806937.png)

在control层调用service层的save方法，service层调用dao层save方法

#### control

```java
public class UserControl {
    private UserService userService;

    public UserService getUserService() {
        return userService;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    public void saveUser()
    {
        userService.saveUser();
    }
}
```



#### service

类

```java
public class UserServiceImpl implements UserService {

    private UserDao userDao;

    public UserDao getUserDao() {
        return userDao;
    }

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void saveUser()
    {
        userDao.saveUser();
    }
}
```

接口

```java
public interface UserService {
    void saveUser();
}
```

#### dao

类

```java
public class UserDaoImpl implements UserDao {
    public void saveUser(){
        System.out.println("保存成功");
    }
}
```

接口

```java
public interface UserDao {
    void saveUser();
}
```



#### xml配置文件

```java
<bean id="userControl" class="com.java.control.UserControl">
    <property name="userService" ref="userService"></property>
</bean>
<bean id="userDao" class="com.java.dao.impl.UserDaoImpl">
</bean>
<bean id="userService" class="com.java.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
```

## 基于xml自动装配

**只适用于对象，不可用于基本变量**

byType可根据其中所需的类，自动寻找；若出现多个相同的类，会报错（一般如果想调用多个类对象，可将其定义为多例）

byName根据id寻找，id与方法名相同

```java
    <bean id="userControl" class="com.java.control.UserControl" autowire="byType">
    </bean>
    <bean id="userDao" class="com.java.dao.impl.UserDaoImpl">
    </bean>
    <bean id="userService" class="com.java.service.impl.UserServiceImpl" autowire="byType">
    </bean>
```

### 基于注解管理bean

#### 注解与扫描

在相应的类注解，把类当成bean

```java
@Controller //控制层注解
@Service //业务层注解
@Repository //数据层注解
@Component //普通组件
```

如果有多个类型的不同类名的bean：

1，则可用@Primary在想要的类上，则会优先使用该类

2，在定义上用@Autowired和Qualifier（bean id）指定

3，在定义上用@Resource（bean id）指定



配置文件扫描

```java
<context:component-scan base-package="com.java"></context:component-scan>
```

#### 主动装配

##### @Autowired

1->在要装配的相应的对象、成员变量之前，此时不需要set方法

2->在set方法前

3->在有参构造函数前



##### @Autowired注解原理

*a>默认通过byType的方式，在IOC容器中通过类型匹配某个bean为属性赋值*

b>若有多个类型匹配的bean，此时会自动转换为bvName的方式实现自动装配的效果，即将要赋值的属性的属性名作为bean的id匹配某个bean为属性赋值
*c>若byType和byName的方式都无妨实现自动装配，即IOC容器中有多个类型匹配的bean*且这些bean的id和要赋值的属性的属性名都不一致，此时抛异常:ouniqueBeanDefinitionExceptior（可用@Qualifier（）指定bean为其赋值）





#### beanid

```java
@Controller("kz")
public class UserControl {
    @Autowired
    private UserService userService;

    public UserService getUserService() {
        return userService;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    public void saveUser()
    {
        userService.saveUser();
    }
}
```

beanid默认为类的小驼峰，也可在注解后添加别名

此类的id默认为userControl，添加注解后别名为kz

## AOP（面向切面编程）

### 代理模式

**代理模式-静态代理**（静态代理可将核心代码和日志代码隔开）

在实现类中实现方法，在proxy中实现日志代码

```java
public class CalculatorImpl implements Calculator{
    @Override
    public int add(int i, int j) {
        int s=i+j;
        return s;
    }
}
```



```java
public class CalculatorProxy implements Calculator{

    private Calculator calculator;

    public CalculatorProxy(Calculator calculator) {
        this.calculator = calculator;
    }

    @Override
    public int add(int i, int j) {
        System.out.println("日志:参数为"+i+","+j);
        int result=calculator.add(i,j);
        System.out.println("日志:结果为"+result);
        return result;
    }
}
```



**代理模式-动态代理**

实现一个代理工厂类

```java
public class ProxyFactory {


    private Object target;

    public ProxyFactory(Object target) {
        this.target = target;
    }

    public Object getTarget()
    {
        ClassLoader classLoader = this.getClass().getClassLoader();//类装配器
        Class<?>[] interfaces = target.getClass().getInterfaces();//接口
        InvocationHandler h=new InvocationHandler()//实现方法 
        {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                System.out.println("方法"+method.getName()+"参数"+ Arrays.toString(args));
                Object invoke = method.invoke(target, args);//args为参数
                System.out.println("方法"+method.getName()+"参数"+ Arrays.toString(args));

                return invoke;
            }
        };


        return Proxy.newProxyInstance(classLoader,interfaces,h);
    }
}
```

Test类

```java
public void test()
{
    ProxyFactory proxyFactory =new ProxyFactory(new CalculatorImpl());
    Calculator target = (Calculator) proxyFactory.getTarget();
    int add = target.add(8,8);
    System.out.println(add);
}
```

### 基于注解的AOP



Aspect类

```java
package com.java;


import com.sun.jdi.ArrayType;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import java.sql.Array;
import java.util.Arrays;

@Component
@Aspect
public class AopAspect {

    @Pointcut("execution(* com.java.CalculatorImpl.*(..))")
    public void Pointcut(){}

    @Before("Pointcut()")
    public void BeforeLogger(JoinPoint joinPoint)
    {
        Signature signature = joinPoint.getSignature();
        Object[] args = joinPoint.getArgs();
        System.out.println("这是前置通知,方法名为"+signature.getName()+"参数为"+ Arrays.toString(args));
    }

    @After("Pointcut()")
    public void AfterLogger(JoinPoint joinPoint)
    {
        Signature signature = joinPoint.getSignature();
        System.out.println("这是后置通知,方法"+signature.getName()+"执行完毕");
    }

    @AfterThrowing(value = "Pointcut()",throwing = "exception")
    public void ExLogger(Exception exception)
    {
        System.out.println("这是报错通知"+"报错的信息是"+exception);
    }

    @AfterReturning(value = "Pointcut()",returning = "result")
    public void ReturnLogger(Object result)
    {
        System.out.println("这是返回通知"+"返回的结果为"+result);
    }

    @Around("Pointcut()")
    public Object aroundAop(ProceedingJoinPoint joinPoint)
    {
        Object proceed=null;
        try {
            System.out.println("围绕通知的前置");
            proceed = joinPoint.proceed();
            System.out.println("围绕通知的返回");
        } catch (Throwable e) {
            throw new RuntimeException(e);
        }
        finally {
            System.out.println("围绕通知的后置");
        }
        return proceed;
    }
}
```

xml文件

```xml
<context:component-scan base-package="com.java"></context:component-scan>//注解，扫包
<aop:aspectj-autoproxy />开启aop
```

测试类

```java
public class Springtest1 {
    @Test
    public void test()
    {
        ApplicationContext ioc = new ClassPathXmlApplicationContext("Springaop.xml");
        Calculator bean = ioc.getBean(Calculator.class);
        bean.add(2,3);
    }
}
```



































### JdbcTmepelate



xml文件

**jdbc.properties资源**：

```java
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/demo?serverTimezone=UTC
jdbc.username=root
jdbc.password=root
```

```xml
<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>

<bean id="DataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
//配置数据库连接池可用于连接数据库

<bean class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="DataSource"></property>
</bean>
//配置JdbcTempalate模板，可用于操作数据库
```

实现类



update语句可实现添加删除修改

query可实现查询，又分为queryForObject，query。见以下代码注释

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-jdbc.xml")
public class Springtest1 {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void test()
    {
        String sql="insert into user values(?,?,?,?,?)";
        jdbcTemplate.update(sql,2,"cyz",18,"男",177);

    }
    
        @Test
    public void Selecttest()
    {
        String sql="select * from user where id=?";
        String sql2="select * from user";
        String sql3="select count(*) from user";
        User user=jdbcTemplate.queryForObject(sql,new BeanPropertyRowMapper<>(User.class),1);
        //查询单条数据
        List<User> query = jdbcTemplate.query(sql2, new BeanPropertyRowMapper<>(User.class));
        //查询全部数据
        Integer anInt = jdbcTemplate.queryForObject(sql3,Integer.class);
        //查询数据总数
        System.out.println(user);
        query.forEach(System.out::println);
        System.out.printf(anInt.toString());
    }
}
```
