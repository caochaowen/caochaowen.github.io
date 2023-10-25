---
title: Java框架
date: 2023-10-16 11:15:10
tags: 
	- Java
	- Spring
categories: 后端
comments: true
---


# Spring

> ## 学习主要参考spring官方文档

> ## 重点在于(IOC)控制反转和(AOP)面向切面的编程

重要的是Spring框架的控制反转（IoC）容器。对Spring框架的IoC容器进行彻底的处理之后，将全面介绍Spring的面向方面的编程（AOP）技术。Spring框架拥有自己的AOP框架，该框架在概念上易于理解，并且成功解决了Java企业编程中AOP要求的80％的难题。



<!--more-->

## 1、IOC容器

==思想==

> ioc思想预演

```java
public class UserServiceImpl implements  UserService {
    private UserDao userDao=new UserDaoImpl();
    //利用set进行动态实现值的注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    public void getUser(){
        userDao.getUser();
    }
}
```

- 通过set方法，程序主动性的移除，程序从主动创建变成被动注入。
- 简单说对于程序的主动权转移了，由程序员主动选择创建对象变成将对象创建的控制权移交给第三方，由用户主动选择

通过这种思想，程序员不再需要管理对象的创建，变成被动的接收对象。耦合度降低了

**控制反转是通过描述（xml或注解）并通过第三方去生产或获取对象的方式。在spring中是通过ioc容器实现控制反转，实现方法是依赖注入DI**

> 官方解释

```
IoC也称为依赖注入（DI）。在此过程中，对象仅通过构造函数参数，工厂方法的参数或在构造或从工厂方法返回后在对象实例上设置的属性来定义其依赖项（即，与它们一起使用的其他对象） 。然后，容器在创建bean时注入那些依赖项。此过程从根本上讲是通过使用类的直接构造或诸如服务定位器模式之类的机制来控制其依赖项的实例化或位置的bean本身的逆过程（因此称为Control Inversion）。
```



> spring的ioc简单实现

**实体类**

```java
public class Hello {
    public String str;

    public Hello(String str) {
        this.str = str;
    }
    public Hello() {
    }
    public String getStr() {
        return str;
    }
    public void setStr(String str) {
        this.str = str;
    }
    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}

```

**xml配置容器**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
	<!--注册对象-->
    <!--spring创建bean对象
    正常：bean=new 对象（）;
    id=>变量名
    class=>new出的对象
    本质是通过反射
    property=>相当于调用了set方法设置属性值
    -->
    <bean id="hello" class="com.ccw.pojo.Hello">
        <property name="str" value="小曹"></property>
    </bean>
</beans>
```

**测试类**

```java
@Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //从容器中获取bean对象
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello);
    }
```

**总的说对象由spring创建，管理，控制**

 



### ioc创建对象的方式

1. 默认通过无参构造创建对象（在读取xml文件后就创建了）

   ```xml
   <beans>
           <bean id="user" class="com.ccw.pojo.User">
               <property name="name" value="小曹"></property>
           </bean>
       </beans>
   ```

   

2. property调用了set方法

3. 通过有参构造创建

   1. 通过索引值传参调用有参构造

      ```xml
      <beans>
              <bean id="user" class="com.ccw.pojo.User">
                  <constructor-arg index="0" value="小曹"/>
              </bean>
          </beans>
      ```

   2. 通过参数类型传值调用有参构造方法（不推荐使用）

      ```xml
      <beans>
              <bean id="user" class="com.ccw.pojo.User">
                  <constructor-arg type="java.lang.String" value="小曹"/>
              </bean>
          </beans>
      ```

   3. 直接通过参数名设置值

      ```xml
      <bean id="user" class="com.ccw.pojo.User">
              <constructor-arg name="name" value="小曹"/>
          </bean>
      ```

==总结==

在加载配置文件的时候，容器中管理的所有对象就已经被初始化了



## 2、spring配置

###  2.1、别名

```xml
<!--alias=>别名-->
    <alias name="user" alias="user2"/>


<!--name可取别名，而且可以取多个-->
    <bean id="user" class="com.ccw.pojo.User" name="user2,user3">
        <constructor-arg name="name" value="小曹"/>
    </bean>
```

### 2.2、import

用于团队开发，可合并多个配置文件

```
<!--合并配置包-->
<import resource="bean.xml"/>
<import resource="bean.xml1"/>
<import resource="bean.xml2"/>
```



## 3、依赖注入（DI）

### 3.1、构造器注入

就是上面说的有参无参注入

### 3.2、set方式注入【重点】前面说的思想演示代码

- 依赖注入：set注入
  - 依赖：bean对象的创建依赖于容器
  - 注入：bean对象中的所有属性，由容器注入

【注入环境搭建】

1. 复杂类型

   ```java
   public class Address {
       private String address;
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
   }
   
   ```

   

2. 真实测试对象

   ```java
   public class Student {
       private String name;
       private Address address;
       private String[] books;
       private List<String> hobbies;
       private Map<String,String> card;
       private Set<String> games;
       private String wife;
       private Properties info;
   }
   ```

   

3. xml配置文件

   ```xml
   <bean id="student" class="com.ccw.pojo.Student">
           <property name="name" value="小曹"/>
       </bean>
   ```

   

4. 测试类

   ```java
   @Test
       public void test(){
           ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
           Student student = (Student) context.getBean("student");
           System.out.println(student.getName());
       }
   ```

   

5. 注入不同类型值

   - 普通值注入

     ```xml
     <bean id="student" class="com.ccw.pojo.Student">
             <property name="name" value="小曹"/>
         </bean>
     ```

   - 引用类型注入

     ```xml
     <bean id="address" class="com.ccw.pojo.Address"/>
     <bean id="student" class="com.ccw.pojo.Student">
             <property name="address" ref="address"/>
         </bean>
     ```

   - 普通数组注入Array

     ```xml
     <property name="books">
                 <array>
                     <value>小贩</value>
                     <value>呐喊</value>
                     <value>彷徨</value>
                     <value>骆驼祥子</value>
                 </array>
             </property>
     ```

   - List集合注入

     ```xml
     <property name="hobbies">
                 <list>
                     <value>网球</value>
                     <value>编程</value>
                     <value>游戏</value>
                 </list>
             </property>
     ```

   - map集合注入

     ```xml
     <property name="card">
                 <map>
                     <entry key="性别" value="男"/>
                     <entry key="特征" value="牛"/>
                 </map>
             </property>
     ```

   - set集合注入

     ```xml
     <property name="games">
                 <set>
                     <value>lol</value>
                     <value>csgo</value>
                 </set>
             </property>
     ```

   - property集合注入

   ```xml
   <property name="info">
               <props>
                   <prop key="小学">城北小学</prop>
                   <prop key="初中">城北中学</prop>
               </props>
           </property>
   ```

   

### 3.3、【拓展】命名空间注入（p标签和c标签）

> p-namespace

1. 导入p空间头文件约束

```xml
 xmlns:p="http://www.springframework.org/schema/p"
```

2. 通过  **property**  直接注入

```xml
<!--p-namespace-->
    <bean id="user1" class="com.ccw.pojo.User" p:name="小曹"/>
```

> c-namespace

1. 导入c空间头文件约束

```xml
xmlns:c="http://www.springframework.org/schema/c"
```

2. 通过有参构造 **constructor-arg** 注入

```xml
<!--c-namespace-->
    <bean id="user2" class="com.ccw.pojo.User" c:name="小曹"/>
```

### bean的作用域

![image-20210404090304795](Spring.assets/image-20210404090304795.png)



1. 单例模式（spring默认机制）

   ```xml
   <bean id="user1" class="com.ccw.pojo.User" p:name="小曹" scope="singleton"/>
   ```

2. 原型模式：每次从容器中get一个新对象，产生新的对象

   ```xml
   <bean id="user1" class="com.ccw.pojo.User" p:name="小曹" scope="prototype"/>
   ```

3. 其余的只能在web开发中使用到





## 4. Bean的自动装配

- 自动装配式spring满足bean依赖的方式
- 具体就是通过autowired自动在容器中通过name或type寻找bean对象并装配到autowired属性所在对象中



**spring三种装配方式**

1. 在xml中显示的配置
2. 在java中显示的配置
3. 隐式的自动装配

### 4.1 xml配置自动装配

- pojo

```java
public class People {
    public String name;
    public Dog dog;
    public Cat cat;
}
```

- xml容器
  - byntype

```xml
<bean id="people" class="com.ccw.pojo.People" autowire="byType">
        <property name="name" value="小曹"/>
    </bean>
```

- 测试

```java
@Test
    public void test1(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
        People people = context.getBean("people",People.class);
        people.getDog().shout();
        people.getCat().shout();
        System.out.println(people.name);
    }
```



### 4.2、注解实现自动装配

jdk1.5之后支持注解，spring2.5后支持注解

```
The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML. The short answer is “it depends.” 
```

官当文档说注解比xml配置更好

想用注解准备

1. 导入约束头文件：context约束
2. 开启注解支持：<context:annotation-config/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    <context:annotation-config/>
</beans>
```

3. 注解位置==@Autowired==（byName查找）

可直接用在属性上，也可以在set上使用。



使用autowired可以不用写set方法，前台你这个被装配的属性要在ioc容器中存在，且名字一致

```java
public class People {
    public String name;
    @Autowired
    public Dog dog;
    @Autowired
    public Cat cat;
}
```

拓展

```java
@NUllable 字段标记后，此字段可为null
```

```java
public @interface Autowired {
    boolean required() default true;
}
```

```java
@Autowired(required = false) 表示此字段能为空
```

==@Qualifier==（配合autowired使用）

如果自动装配的环境比较复杂，@Autowired注解无法完成时可配合@Qualifier(value = "cat")配置指定装配对象

==@Resource==(java的原生注解，功能强大，用的少)

首先找name属性，name属性没有则根据类型type查找，@Resource（name="xxx"），name字段可指定名称

==@Component==

放在类上，表示此类beispring托管，是一个bean。

==@Scope==

选择单例或原型模式产生对象

## 5、使用注解开发

> 首先确保注解包导入，也就是AOP包和context包

![image-20210405145808142](Spring.assets/image-20210405145808142.png)

xml文件中配置注解扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    <!--注解扫描-->
    <context:component-scan base-package="com.ccw.pojo"/>
    <!--启用注解-->
    <context:annotation-config/>
</beans>
```

注解代码

```java
//Component组件，spring托管组件
@Component
public class User {
    //实现属性注入的注解
    @Value("小v")
    public String name;
}
```

**衍生注解**（spring托管注解，和Component组件功能相同）

==@Component==的衍生注解，分别用于web的mvc三层架构

- Dao层【@Repository】

- Service层【@Service】

- Controller层【@Controller】

javaConfigration实现注解(类似application.xml)

```java
//支持xml中的配置，本身就是一个Componont组件，spring新的特性
@Configuration
@ComponentScan("com.ccw")
//合并其他config类
@Import(Config2.class)
public class Config {
    @Bean
    public User getUser(){
        return new User();//返回注入到bean的对象
    }
}

```



## 6、代理模式（23种模式之一）

> 分为静态代理和动态代理

### 6.1、静态代理



**角色分析：**

- 抽象角色（出租事件）：一般用接口或抽象方法实现
- 真实角色（房东）：被代理角色
- 代理角色（中介）：代理真实角色，代理后，可附加一些方法
- 客户（租客）：访问代理对象



==代码范例==

出租抽象事件

```java
public interface Rent {
    public void lease();
}
```

房东，被代理对象

```java
//房东类
public class Host implements Rent {
    public void lease() {
        System.out.println("房东出租成功！");
    }
}
```

中介，代理对象

```java
public class Proxy implements Rent {

    private Host host;


    public Proxy(Host host) {
        this.host = host;
    }

    public Proxy() {
    }

    //代理出租
    public void lease() {
        agency();
        watch_house();
        host.lease();
    }

    public void agency(){
        System.out.println("收取中介费");
    }

    public void watch_house(){
        System.out.println("看房");
    }
}
```

客户，测试

```
public class client {
    @Test
    public void getHouse(){
        Host host=new Host();
        Proxy proxy=new Proxy(host);
        proxy.lease();
    }
}
```



**静态代理模式优点**：

- 分离真实角色，真实角色更加存粹，只需要专注完成自己特有的业务方法
- 公共业务交给代理角色，实现业务公共和私有的分离
- 公共业务拓展的时候方便管理

**缺点**：

一个真实角色就会产生一个代理角色。大大增加了代码量（用动态代理可解决）

### 6.2、动态代理

- 动态代理和静态代理实现角色相同
- 动态代理的代理对象也就是Proxy类是动态生成的，而不是直接继承创建写出来的类
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口：jdk动态代理【本次实现的动态代理】
  - 基于类：cglib
  - java字节码实现： javasist

接口实现主要两大类 ==Proxy类==：代理        ==InvocationHandler==：调用处理程序（接口）

roxy

==代码==（其他类和静态代理是一样的，只有代理类，和代理对象的调用不一样）

- 动态代理类**Proxy**

```java
public class Invocation implements InvocationHandler {
    Rent rent;

    public Rent setRent(Rent rent) {
        this.rent = rent;
        return rent;
    }
    
    //创建代理类通过代理接口
    public Object getProxy(){
        /**newProxyInstance参数详解
         * 第一个参数=>动态代理类的加载
         * 第二个参数=>被代理事件的接口，注是被代理抽象事件而不是被代理真实对象
         * 第三个参数=>本类
         */
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),rent.getClass().getInterfaces(),this);
    }

    //本类返回的对象就是这个方法返回的，此方法通过invoke反射返回了被代理的真实对象的整个类的方法
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //通过反射实现代理对象的业务
        return method.invoke(rent,args);
    }
}
```

- 调用类**Client**

```java
@Test
    public void test(){
        //创建真实被代理对象
        Host host=new Host();
        //创建动态代理方法
        Invocation invocation = new Invocation();
        /**
         * 向动态代理方法中放入真实被代理对象，返回的对象就是动态代理对象，
         * 此时也可以看成是一个没有单独创建类的静态代理
         */
        Rent proxy= invocation.setRent(host);
        //通过反射实现被代理类的对象
        proxy.lease(); 
    }
```



## 7、AOP面向切面

> 向程序中插入切入点，并在切入点放入扩展业务，增加的功能与业务逻辑无关，如日志，安全，缓存，事物等等。。。。



- 横切关注点：跨越应用程序多个模块的方法或功能
- 切面：横切关注点被模块化的特殊对象。即一个类
- 目标：被通知对象
- 代理：向目标对象应用通知之后创建的对象
- 切入点：切面通知执行的地点的定义
- 连接点：与切入点匹配的执行点







SpringAOP中，支持物种类型的Advice







==代码==

**准备先导入aspectj的包**

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
```

**主要的业务逻辑类（被切入的类）**

```java
public class UserServiceImpl implements UserService {
    public void add() {
        System.out.println("实现添加方法");
    }

    public void delete() {
        System.out.println("实现删除操作");
    }

    public void query() {
        System.out.println("实现查询操作");
    }

    public void update() {
        System.out.println("实现更新操作");
    }
}
```

**切入的类（增加的功能）**

```java
public class Log implements MethodBeforeAdvice {
    /**
     * @param method 要执行的目标对象的方法
     * @param args 参数
     * @param target 目标对象
     * @throws Throwable
     */
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"方法被执行了");
    }
}

public class AfterLog implements AfterReturningAdvice {
    public void afterReturning(Object o, Method method, Object[] objects, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"方法被执行了");
    }
}

```

**添加到spring管理（xml配置文件）**

```xml
<!--使用原生的Spring AOP 的API-->
    <!--配置aop的环境约束头文件-->
    <aop:config>
        <!--创建切入点，execution：表达式表示被切入的点的位置-->
        <aop:pointcut id="point" expression="execution(* com.ccw.service.UserServiceImpl.*(..))"/>
        <!--向切入点添加切入对象-->
        <aop:advisor advice-ref="log" pointcut-ref="point"/>
        <aop:advisor advice-ref="afterlog" pointcut-ref="point"/>
    </aop:config>
```

测试类

```java
@Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userservice = context.getBean("userservice", UserService.class);
        userservice.add();
    }
```