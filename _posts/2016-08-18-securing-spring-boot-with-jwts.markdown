---
layout: post
title: "Securing Spring Boot with JWTs"
description: Learn how to set up a Spring Boot project, using Java, and securing it with JWTs. 
date: 2016-08-18 00:14
author: 
  name: Dylan Meeus
  url: http://www.twitter.com/DylanMeeus
  mail: meeusdylan@hotmail.com
  avatar: https://en.gravatar.com/userimage/67902576/25a6df871f404218103361055634917f.jpeg
design: 
  bg_color: #FF00FF
  image: https://assets.toptal.io/uploads/blog/category/logo/59/spring.png
tags:
- Spring Boot
- JWT
- Java
---

-----
**TL;DR** Spring Boot is a technology making it easy to create Java and Groovy applications utilizing the full power of spring, with minimal setup. It allows you to create applications that 'just run' without the hassle, and even the project setup becomes a breeze. Keep on reading to find out how to set up a fully-functioning project and secure it with JWT in almost no time!
-----

## Spring Boot Overview

Spring Boot is another technology from Pivotal, known for giving us the powerful Spring framework, which features the full power of Spring in an easily accessible framework. With Spring Boot, they offer an easy way to set up a project and get it running in no-time. They took the approach of convention over configuration, meaning that you don't need to spend a lot of time struggling with spring configuration. Together with having an embedded Tomcat, Jetty or undertow, a large part of possible deployment issues have been taken away, causing most applications to 'just run'. 

Above all, Spring Boot is meant to make it *easy* to get a project running, which starts all the way at the beginning, by providing multiple ways to set up a Spring Boot project. Let's jump right into setting up our project and getting our 'Hello World'!

If you can't wait to see the result, feel free to check out the full code on this [github repository](https://github.com/DylanMeeus/springboot_jwt_blog).

## Setting up the project
### Getting an application
There are various ways to get started with a Spring Boot project. They have provided a [quick start](http://start.spring.io/) and a CLI-tool. Apart from using the quick start or the Spring Boot CLI, you could also set up a project with Spring Boot manually using either Maven or Gradle. In this post we will set up a project using the provided quick start, which can be found at [start.spring.io](http://start.spring.io/).

When you head over to the quick start, you are presented with a few options on the main page. We will generate a *maven project* with the latest version of Spring Boot (1.4.0 at the time of writing). With the Project Metadata you can set up the standard properties of a maven artifact, but we can just leave it at the default for now.

Next, we can Search for dependencies. Searching for dependencies is a handy feature once you have used Spring Boot several times and are aware of the available dependencies. Because this is our first project however, we can click on the link at the bottom that says *switch to full version*. After doing this, we have a webpage with checkboxes for all the available dependencies. Because we want to create a web-project, we can tick that checkbox under the *web* heading.

As you can tell from this page, there are a lot of dependencies available which work with Spring Boot out-of-the-box. Many of the common technologies are provided here, such as dependencies for HATEOAS, JPA, MongoDB, Thymeleaf and many many more. If you are going to use one of the common Java technologies, chances are that it can easily be included in your Spring Boot project.

For now, all we really need is the *Web* dependency, which gives us a Tomcat server and the Spring MVC framework. Once you click on the *Generate Project* button, a download will start containing a starting project for your selected setup. Setting up a Spring Boot project really is just a matter of minutes!


{% include tweet_quote.html quote_text="Setting up a Spring Boot project is a matter of minutes!" %}

## Our first Hello World

Once we have downloaded our zip file, the project can be imported in your favourite editor. We are ready to start coding now, but some code is already generated for us. Let's take a look at some of the generated code first to understand what Spring Boot has done for us.

_Look at maven structure / default classes_ 

## Securing it with JWT
### Info about JWT - Link to some other resources
### Create a mock-project with restricted routes. 

## Advantages of Spring Boot
++ Mention microservices here ++

## Conclusion




