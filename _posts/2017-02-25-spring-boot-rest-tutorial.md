---
layout: post
title: Spring Boot Rest Tutorial
date: 2017-01-22 16:10
categories: spring-boot
published: false
---

# Spring Boot Rest Tutorial

This tutorial will focus on spring-boot and rest services.  We will go over the basics of setting up a spring boot application and implementing rest services on that application.

# Prerequisites
Java version : 1.8
Maven version 3.1 or greater
Your favorite IDE

# Getting Started

Let's create a simple maven project and edit the pom.xml to include the spring dependencies.

```xml
    
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <groupId>matt.blog</groupId>
      <artifactId>quote-rest-service</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>Project created to learn about spring boot</name>
      <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.1.RELEASE</version>
      </parent>
      <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      <dependencies>
        <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
      </dependencies>
    </project>

```

### Let's Talk Dependencies
  We have added the `spring-boot-starter-parent` dependency as a parent project and named a version there of the latest release `1.5.1.RELEASE`.   At this point we havn't included any dependencies into our project classpath.  To add the required dependency for RESTful services functionality we will need to add the `spring-boot-starter-web`.  
  
  
### Let's Add Main Method
Spring applications rely on a `SpringApplication.run(Main.class, commandLineArgs)` method as the starting point for Spring applications.  Let's add that class now.

```java

    package io.matt.blog;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class RestServiceApp {

      public static void main(String[] args) {
        SpringApplication.run(RestServiceApp.class, args);

      }

    }
 

```

### Now Let's Run Our Application
With only our pom file and a main method we start our spring boot application running on an embedded tomcat server.  We don't need a web.xml or a spring-context.xml file.  To run the application we can simple `Run As..` a java application in your IDE or we can execute on the command line mvn spring-boot:run.

To validate that our application is starting up correctly we should see a print out of the following:

```
  
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.1.RELEASE)
 ...Some other logs will be printed but eventually we should see..
2017-02-25 13:52:49.944  INFO 1288 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-02-25 13:52:50.024  INFO 1288 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2017-02-25 13:52:50.031  INFO 1288 --- [           main] io.matt.blog.RestServiceApp              : Started RestServiceApp in 2.844 seconds (JVM running for 3.168)



```



