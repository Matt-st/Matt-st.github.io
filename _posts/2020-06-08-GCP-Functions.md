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

### Let's create a simple unit test.

