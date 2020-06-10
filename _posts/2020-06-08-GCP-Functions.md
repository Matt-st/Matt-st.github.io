---
layout: post
title: "Building a GCP Function"
date: 2020-06-05
categories: Java, GCP, Serverless, Function
published: false
---


## Getting Started Building a GCP Function

This tutorial will focus on getting up and running quickly with a GCP Function.  It will start off by following the getting started guide provided by [GCP Functions Java](https://cloud.google.com/functions/docs/first-java). Then we will build, run, unit test, and integration test the function.  After that we will customize our function code for our needs using a Movie application design.  The code used in this tutorial will be available on my [github]()

## Prerequisites
* Java version : 1.8
* Maven version 3.1 or greater
* Your favorite IDE

## Getting Started

To get started follow the directions a building a function provided on the [GCP Functions Java tutorial page.](https://cloud.google.com/functions/docs/first-java)

At this point you should be able to do the following tasks:
* Run `mvn compile` successfully
* Run `mvn function:run` successfully this starts your function locally like a simple http endpoint so that it can recieve calls via http
* Open a browser and hit `localhost:8080\` and receive a response of `Hello World!`


### Why use GCP functions

A GCP function is similar to a AWS Lambda or an Azure Functions.  The concept is to run these as serverless pieces of code that will be spun when invoked/triggered by an event.  They have an appeal as being cheaper, easy to reason about as an artifact of being small, and we don't have to worry about managing servers.  There are plenty of other reasons as well but I'll leave off with those three.

### Review key points in the code

Below is the entirety of the getting started code:

1. We can see that the class implements the HttpFunction interface which is a functional interface see the [code here](https://github.com/GoogleCloudPlatform/functions-framework-java/blob/master/functions-framework-api/src/main/java/com/google/cloud/functions/HttpFunction.java)

2. We implement that `void service(HttpRequest request, HttpResponse response)` function which is the entry point of any call to our GCP function.



```java

public class HelloWorld implements HttpFunction {
  // Simple function to return "Hello World"
  @Override
  public void service(HttpRequest request, HttpResponse response)
      throws IOException {
    BufferedWriter writer = response.getWriter();
    writer.write("Hello World!");
  }
}

```


### Key dependencies

1.  We have the GCP functions api dependency which is linked [here](https://github.com/GoogleCloudPlatform/functions-framework-java/blob/master/functions-framework-api/src/main/java/com/google/cloud/functions/HttpFunction.java)

```xml
    <dependency>
      <groupId>com.google.cloud.functions</groupId>
      <artifactId>functions-framework-api</artifactId>
      <version>1.0.1</version>
      <scope>provided</scope>
    </dependency>

```

2. GCP functions Plugin that allows us to run our functions with the `mvn function:run` command.

```xml

 <plugin>
	<!--
	  Google Cloud Functions Framework Maven plugin

	  This plugin allows you to run Cloud Functions Java code
	  locally. Use the following terminal command to run a
	  given function locally:

	  mvn function:run -Drun.functionTarget=your.package.yourFunction
	-->
	<groupId>com.google.cloud.functions</groupId>
	<artifactId>function-maven-plugin</artifactId>
	<version>0.9.2</version>
	<configuration>
	  <functionTarget>functions.HelloWorld</functionTarget>
	</configuration>
</plugin>
```

### Where we are now
At this point we have simply explained in slightly more detail what the hello world GCP function is actually doing.  We have highlighted the dependencies, plugins, commands, and the code portion that will be common across all java GCP functions that anyone will create.

Next we will customize this function to something slightly more interesting. But first let's add a unit test for our hello world GCP function.

### Let's create a simple unit test

1. Create the directory structure for the tests

`src/test/java/functions/`

2. Create the test class
`HelloWorldTest.java`

3. Add the junit 5 dependency to the `pom.xml`

```xml
<!-- https://mvnrepository.com/artifact/org.junit.jupiter/junit-jupiter-api -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.6.2</version>
    <scope>test</scope>
</dependency>


```



