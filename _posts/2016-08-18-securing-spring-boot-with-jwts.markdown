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

Spring Boot is another technology from Pivotal, known for giving us the powerful Spring framework, which features the full power of Spring in an easily accessible package. With Spring Boot, they offer an easy way to set up a project and get it running in no-time. They took the approach of convention over configuration, meaning that you will spend a lot less time struggling with the setup. Together with having an embedded Tomcat, Jetty or undertow, a large part of possible deployment issues have been taken away, causing most applications to 'just run'.

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

## Our first application

Once we have downloaded our zip file, the project can be imported in our favourite editor. At this point, some code is already generated for us. Let's take a look at some of the generated code first to understand what Spring Boot has done for us.

As this is a maven project, we will examine the _pom.xml_ file first. The top of the file is pretty much a standard pom file. It identifies your project by the `groupId` and the `artifactId`. Your project also has a name and a description. Next, there is a reference to a parent. This is the parent of all Spring Boot starters, and contains further dependencies needed for the base of Spring Boot.

This is followed by the properties, which tell you something more about your project. Because we have chosen java 8, the properties also reflect this in the `java.version` tag.

Here comes the interesting part:

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
     	</dependencies>

     	<build>
     		<plugins>
     			<plugin>
     				<groupId>org.springframework.boot</groupId>
     				<artifactId>spring-boot-maven-plugin</artifactId>
     			</plugin>
     		</plugins>
     	</build>

The dependencies you see here are the ones that where selected during the setup. Because we just selected the Web dependency, we will see both Web and Test. Each project has tests, or at least, is set up to contain tests. Though it might look like there are not a lot of dependencies being pulled in, that's an illusion due to how Spring Boot works. The `spring-boot-starter-` dependencies that you enter here actually load in a whole lot of other dependencies they need. Because this is invisible to us the pom.xml file looks quite clean, and all the management of those dependencies is taken care of for us as well.

Next, there is one plugin, the `spring-boot-maven-plugin`. This will let us build and deploy the entire application with one simple maven command `mvn spring-boot:run`. If you try to run this now, there won't be anything interesting yet. After running that command, you can go to `localhost:8080`, though all you will see is a "Whitelabel Error Page". But, if all went well, the compilation worked without any problems and your server is up and running. Time to start doing something interesting with it!

## Creating a web application

In this part, we will set up a small application, which will accept REST-requests on various paths and return data. To keep this example concise, all the information will be statically coded. At first, we will just expose all the data to everyone. Next we will secure some routes with a JWT and provide a login mechanism.

For starters, we will just create a mapping against the root (`/`) of our web-server. So we can verify that this is working, and afterwards we can add more specific routes for the various functions that we will offer. When you want to make methods against paths, you need to create a `RestController`. For now we will use our only class for this, we will add the `RestController` and `EnableAutoConfiguration` annotations to this class. If you followed this blog exactly, the class should be called `DemoApplication`. If not the name might differ, so just look for the only `java` class with a main method.

     package com.example;

     import org.springframework.boot.SpringApplication;
     import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
     import org.springframework.web.bind.annotation.RequestMapping;
     import org.springframework.web.bind.annotation.RestController;

     @RestController
     @EnableAutoConfiguration
     public class DemoApplication {

     	@RequestMapping("/")
     	String hello(){
     		return "hello world";
     	}


     	public static void main(String[] args) {
     		SpringApplication.run(DemoApplication.class, args);
     	}
     }

When we run our application, again by using `mvn spring-boot:run`, we can navigate to `localhost:8080`, and  are presented with a "Hello World". (alternatively to using your browser to test the application, you could use a tool like `curl` or `postman` to send requests to the webserver. Later, when dealing with JWTs, that might actually be the easiest way)


Now we will extend this example a little bit. We will create a new class `UserController` which will be another `RestController`. We will return some (static) JSON data as an example at route `/users` to begin with.

     package com.example;
     import org.springframework.web.bind.annotation.RequestMapping;
     import org.springframework.web.bind.annotation.ResponseBody;
     import org.springframework.web.bind.annotation.RestController;


     @RestController
     public class UserController
     {

         @RequestMapping("/users") /* Maps to all HTTP actions by default (GET,POST,..)*/
         public @ResponseBody
         String getUsers(){
             return "{\"users\":[{\"firstname\":\"Richard\", \"lastname\":\"Feynman\"}," +
                     "{\"firstname\":\"Marie\",\"lastname\":\"Curie\"}]}";
         }

     }

We have some pretty similar code to our original 'Hello World', but now we are returning JSON-encoded data. For this reason, the `@ResponseBody` annotation has been added. With this annotation, when a request specifies in the headers that it wants to accept `application/json`, the data will be returned to the client as JSON. When testing this with postman, the returned data looked like this:

     {
       "user": [
         {
           "firstname": "Richard",
           "lastname": "Feynman"
         },
         {
           "firstname": "Marie",
           "lastname": "Curie"
         }
       ]
     }


## Securing our application with JWT

At this point, our application is exposed to everyone. Anyone can query our webserver and request a list of all the users. Preferably, this is only exposed to people whom are logged in. For this purpose, we will secure our application with JSON Web Tokens.

JWT is a relatively new technology, defined in [rfc-7519](https://tools.ietf.org/html/rfc7519). It defines a compact, URL-safe way of sharing data between parties using a JSON object. The information is signed with either a secret (using an HMAC) or a public/private key-pair using RSA. If you want to learn more about JWTs, [we've got you covered!](https://auth0.com/learn/json-web-tokens/)



### Info about JWT - Link to some other resources
### Create a mock-project with restricted routes. 

## Advantages of Spring Boot
++ Mention microservices here ++

## Conclusion




