---
title: Java基础知识
date: 2023-10-16 11:15:20
tags: 
	- Java
categories: 程序语言
comments: true
---



# java入门

## 编译型和解释型

> 编译型

c/c++直接编译运行，效率高，操作系统。一般需要编译器编译。


> 解释型

解释型语句效率相对较慢



<!--more-->


## 内存分析

> 分析范围包括栈，堆，方法区


## 面向对象和面向过程

### 面向对象

引入了类的思想，以对象组织数据，每个类的内部就是面向过程，但是在宏观上是分类的，功能通过调用类来实现。

从理解的角度是现有对象后有类。对象是具体事物。类是抽象的，对对象的抽象

从代码运行的角度是先有类后有对象。类是对象的模板，先要定好抽象的大概框架模型，再在对象中具体描述实现类定好的框架功能

> 三大特性

**封装**

**继承**

**多态**

### 面向过程

线性处理事件，步骤清晰，一步一步解决问题


## Java构造器

**java在创建对象后对象类的.java文件会生成.class文件，其中.class文件就默认调用了原对象类中的无参构造方法**


> 无参构造器

创建对象时默认调用该构造器



> 有参构造器

只有在调用有参构造函数时会调用，但是定义了有参构造后，如果还想调用无参构造，必须再***显性的定义***一个无参构造。



## 方法的继承

> extends函数

子类继承父类，会永远父类的全部方法，并且在创建子类对象时会默认调用父类的无参构造方法

所有类都默认继承Object类


**super()和this()必须放在方法的第一行**



> 代码演示模块

**person类**

```java
public class Person {
    public Person() {
        System.out.println("Person执行");
    }
    public String name="person_name";
}

```


**student类**

```java
public class Student extends Person{
    public Student() {
        //this()和super()只能放在第一行
        //super();
        System.out.println("Student构造执行");
    }
    public String name="student_name";

    @Test
    public void test(){
        Student student=new Student();
        System.out.println(student.name);
        String name1=super.name;
        String name2=this.name;
        String name3=name;
        System.out.println("super:"+name1+" this:"+name2+" name:"+name3);
    }
}
```

**Test执行结果**

```
Person执行
Student构造执行
Person执行
Student构造执行
student_name
super:person_name this:student_name name:student_name
```




## 多态

> **多态时方法的多态，属性是没有多态的。**
>
> **存在的条件：1.存在父子关系，即有继承，2.父类引用指向子类，3.方法要重写**



> 代码演示

**person类**

```java
public class Person {
    public void run(){
        System.out.println("Person的run方法执行");
    }

}
```

**student类**

```java
public class Student extends Person{
    public void eat(){
        System.out.println("Student的eat方法执行");
    }
    @Test
    public void test(){
        Student s1=new Student();
        Person s2=new Student();
        Object s3=new Student();
        s1.eat();
        //s2.eat();子类新加的方法父类多态对象不能调用，只能转换类型
        s2.run();

    }
}
```

**Test执行结果**

```
Student的eat方法执行
Person的run方法执行
```


## instanceof

> 判断类之间的父子关系



## 匿名类

>  new 类名();

没有对象名不好调用，而且创建之后会自动被回收

作用，利用会马上被回收的特性，一般被用于传值，节省内存空间





# java线程

## 线程的创建

> Thread，Runnable，Callable

## Thread

> 1，自定义线程类继承Thread类
>
> 2，重写run()方法，编写线程执行体
>
> 3，创建线程对象，调用start()方法启动线程



> 代码演示

```java
//1,继承Thread类
public class Thread1 extends Thread{
    //2，重写run方法
    public void run(){
        for (int i=0;i<200;i++)
        System.out.println("run线程执行"+i);
    }

    public static void main(String[] args) {
        for (int i=0;i<200;i++){
            System.out.println("主线程执行"+i);
        }
        Thread1 thread1=new Thread1();
        thread1.start();
    }
}
```

执行结果

```
主线程执行193
主线程执行194
主线程执行195
主线程执行196
主线程执行197
主线程执行198
主线程执行199
run线程执行0
run线程执行1
run线程执行2
run线程执行3
run线程执行4
run线程执行5
run线程执行6
run线程执行7
```





## Lamda表达式

> 作用：避免匿名内部类定义过多

### Functional Interface(函数式接口)

任何接口，只包含一个抽象方法，那么这就是一个函数式接口。（可以理解为这个接口类代表一个函数方法）

> 函数式接口，可以通过lambda表达式来创建接口对象





## 线程的停止

> 建议用标志位停止线程
>
> 不建议使用stop和destroy方法，这些方法被官方废弃，因为这些方法有些安全方面的问题