--
layout: post
title: "Building a Distributed Backend Tutorial Part 1"
date: 2017-01-22
categories: design-patterns, Spring Cloud, Eureka, Netflix, Hystrix
published: false
---


## Building a coffeeshop menu backend
This is the first part of the distributed backend tutorial.   


### What you will learn and do
In part 1 we will build the first application for the coffeeshop architecture.  This application will be a simple restful spring boot app that connects to a cassandra backend.  The restful calls will represent creating, updating, deleting, and retrieving menu items for the coffeeshop menu. 

##### Technologies Used
* Spring Core
* Spring Boot
* Cassandra

##### Tools
Apache Maven 3.2.2   
Java 8   
Git   
IDE of your choice (Demonstrations will be in eclipse)   


## Let's get started!

1. First let's create a new maven project and add the following to the pom.xml

```xml

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.coffeeshop.ordering</groupId>
	<artifactId>cs-query</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>com.jayway.jsonpath</groupId>
			<artifactId>json-path</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-redis</artifactId>
		</dependency>
		<dependency>
			<groupId>redis.clients</groupId>
			<artifactId>jedis</artifactId>
			<type>jar</type>
		</dependency>
	</dependencies>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Finchley.SR2</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<properties>
		<java.version>1.8</java.version>
	</properties>


	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```