---
title: Notes, SpringBoot Guide
categories:
  - Doing
  - Spring
  - 
tags:
  - 
  - 
date: 2018-03-09 17:01:00
toc: true

---
### MVC

The **Model** encapsulates the application data and in general they will consist of POJO.

The **View** is responsible for rendering the model data and in general it generates HTML output that the client's browser can interpret.

The **Controller** is responsible for processing user requests and building an appropriate model and passes it to the view for rendering.

<!--- more --->

* Original MVC

![](http://7xru22.com1.z0.glb.clouddn.com/18-3-9/69812105.jpg)
    
* Spring MVC

![](http://7xru22.com1.z0.glb.clouddn.com/18-3-9/74224334.jpg)


### Dependency Injection (DI)

The technology that Spring is most identified with is the Dependency Injection (DI) flavor of Inversion of Control. 
The Inversion of Control (IoC) is a general concept, and it can be expressed in many different ways.
Dependency Injection is merely one concrete example of Inversion of Control.

What is dependency injection exactly? Let's look at these two words separately. Here the dependency part translates into an association between two classes. For example, class A is dependent of class B. Now, let's look at the second part, injection. All this means is, class B will get injected into class A by the IoC.

### Bean

#### What is Bean
The objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container. These beans are created with the configuration metadata that you supply to the container.

#### Dependency Injection Type & Description

1. Constructor-based dependency injection
Constructor-based DI is accomplished when the container invokes a class constructor with a number of arguments, each representing a dependency on the other class.
2. Setter-based dependency injection
Setter-based DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or no-argument static factory method to instantiate your bean.


#### @Configuration & @Bean Annotations

Annotating **a class** with the @Configuration indicates that the class can be used by the Spring IoC container as a source of bean definitions. 
The @Bean annotation tells Spring that **a method** annotated with @Bean will return an object that should be registered as a bean in the Spring application context. 

#### The @Import Annotation (Optional)

The @Import annotation allows for loading @Bean definitions from another configuration class. 

#### Specifying Bean Scope

The default scope is singleton, but you can override this with the @Scope annotation.

### AOP

#### What is AOP

**Aspect-Oriented Programming** entails breaking down program logic into distinct parts called so-called concerns. 
The functions that span multiple points of an application are called cross-cutting concerns and these **cross-cutting concerns** are conceptually separate from the application's business logic. 
There are various common **good examples of aspects** like **logging, auditing, declarative transactions, security, caching, etc**.

#### Comparison with DI

The key unit of modularity in OOP is the class, whereas in AOP the unit of modularity is the aspect. 
Dependency Injection helps you decouple your application objects from each other and AOP helps you decouple cross-cutting concerns from the objects that they affect. 
AOP is like triggers in programming languages such as Perl, .NET, Java, and others.

Spring AOP module provides **interceptors** to intercept an application. 
For example, when a method is executed, you can add extra functionality before or after the method execution.

### Reference

* [Spring Boot Reference 1.5.9.RELEASE Guide](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/)
* [Spring Boot Docs 1.5.9.RELEASE API](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/api/)
* [Spring Framework Documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/)
* [Spring Web MVC framework](https://docs.spring.io/spring/docs/4.3.13.RELEASE/spring-framework-reference/htmlsingle/#mvc)
* 某忘记名字的网站
* 知乎
 
