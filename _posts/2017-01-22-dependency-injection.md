---
layout: post
title: "Simple Dependency Injection Example"
date: 2017-01-22 16:10
categories: design-patterns
---



#### What is Dependency Injection?
Dependency injection is removing the instantiation of an object from the constructor and either passing it into the constructor to be set or setting it on an object with a setter method.  Really dependency injection simply moves the ownership of creating objects up the dependency tree.  At some point if you are going to apply dependency injection patterns an object will have to be created.  The key is that we can move that creation away such that we can get good tests around the classes which use the dependency among other things.  There are many virtues of using dependency injection which I will list below:

* Makes it easier to follow the Open Closed Principle
* Makes it easier to follow the Single Responsibility Principle
* Reduces the amount of work done in the constructor
* Makes it easy to test classes and methods where you can easily set a test double on the object for mocking

#### Dependency Injection Example
Below you will find an example where are developers are not using dependency injection.  Now we have hardcoded that the person object which NoDependencyInjection class knows about is named matt.  This is a contrived example but it's very representative of what really happens when we don't try to implement DI patterns.

```java
    public class NoDependencyInjection{
    
      private static final String NAME = "Matt";
    
      private Person person;
    
      public NoDependencyInjection(){
        this.person = new Person(NAME);
      }
    
    }
```
Good DI example.  Now again this is very contrived but as you can see any Person object can be set on the DependencyInjection object.  It will be more flexible and just as important much easier to inject a mock Person object such that any functionality can mocked up to help with unit test this DependencyInjection class.

```java
    public class DependencyInjection{
    
      private Person person;
    
      public DependencyInjection(Person person){
        this.person = person;
      }
    
    }
```

## Dependency Injection Frameworks

As you could imagine simplyu moving object instantiation up the object dependency graph will not provide you with true dependency injection.  In the industry developers will mainly use frameworks to achieve dependency injection.  For java the most prevalent frameworks are Spring and Guice.  If you interested in these frameworks please check out there sites at [Spring.io](https://spring.io/ "Spring's Homepage") or [Guice on GitHub](https://github.com/google/guice "Guice GitHub").


## Summary
We discussed briefly about what dependency injection really is.  We have listed some benefits of using dependency injection.  We have also called out some DI frameworks that our popular in the industry at the moment.  Thanks for reading the post!


