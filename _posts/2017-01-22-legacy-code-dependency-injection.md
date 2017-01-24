---
layout: post
title: "Development Stategies for Working in a Legacy Application"
date: 2017-01-22 16:10
categories: design-patterns
---

I have recently been working in a large legacy code base, as such I have been looking to add unit tests.  The problem as many of you will undoubtedly know is that legacy code bases are considered legacy because of the lack of unit testing.  You could be creating brand new code today but if that new feature does not include tests than you aren't doing yourself any favors.  Quickly you will find that your small code base of untested code will become unweildy and each new feature will take a magnitude of time longer to add, precisely because the possibility of breaking your codes behavior.  Hence all of this legacy code can be avoided by writing unit tests.  Alas we are here with our legacy codebase what do we do now?

In the coming weeks I am hoping to introduce a new tutorial series which will rely heavily on design patterns mostly from the gang of four Design Patterns book and also methods for refactoring legacy code which are outlined by Micheal Feathers Working Effectively With Legacy Code.  These books will be precisely what is needed to undo all the legacy code that we have to deal with.  

I will start by going over a few creational design patterns which can be levaraged to create dependency injection such that we find in newer frameworks like Guice or Spring.

To get started I would like to discuss dependency injection to set a base level of understanding on what it it.

##Dependency Injection

####What is Dependency Injection?
Dependency injection is removing the instantiation of an object from the constructor and either passing it into the constructor to be set or setting it on an object with a setter method.  Really dependency injection simply moves the ownership of creating objects up the dependency tree.  At some point if you are going to apply dependency injection patterns an object will have to be created.  The key is that we can move that creation away such that we can get good tests around the classes which use the dependency among other things.  There are many virtues of using dependency injection which I will list below:

* Makes it easier to follow the Open Closed Principle
* Makes it easier to follow the Single Responsibility Principle
* Reduces the amount of work done in the constructor
* Makes it easy to test classes and methods where you can easily set a test double on the object for mocking

####Dependency Injection Example
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


Stay Tuned for updates!
