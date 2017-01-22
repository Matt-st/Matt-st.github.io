---
layout: post
title: "Working with Legacy Code: Dependency Injection"
date: 2017-01-22 16:10
categories: design-patterns
---

I have recently been working in a large legacy code base, as such I have been looking to add unit tests.  The problem as many of you will undoubtedly know is that legacy code bases are considered legacy because of the lack of unit testing.  You could be creating brand new code today but if that new feature does not include tests than you aren't doing yourself any favors.  Quickly you will find that your small code base of untested code will become unweildy and each new feature will take a magnitude of time longer to add, precisely because the possibility of breaking your codes behavior.  Hence all of this legacy code can be avoided by writing unit tests.  Alas we are here with our legacy codebase what do we do now?

In the coming weeks I am hoping to introduce a new tutorial series which will rely heavily on design patterns mostly from the gang of four Design Patterns book and also methods for refactoring legacy code which are outlined by Micheal Feathers Working Effectively With Legacy Code.  These books will be precisely what is needed to undo all the legacy code that we have to deal with.  

I will start by going over a few creational design patterns which can be levaraged to create dependency injection such that we find in newer frameworks like Guice or Spring.

Stay Tuned for updates!
