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

Spring Boot is a technology from Pivotal, known for giving us the powerful Spring framework. With Spring Boot, they offer an easy way to set up a project and get it running in no-time. They took the approach of convention over configuration, meaning that you will spend a lot less time struggling with the setup and can spend more time focussing on what is unique about your project. Together with having an embedded Tomcat, Jetty or undertow, a large part of possible deployment issues have been taken away, causing most applications to 'just run'.

Above all, Spring Boot is meant to make it *easy* to get a project running, which starts all the way at the beginning, by providing multiple ways to set up a Spring Boot project. Let's jump right into setting up our project and getting our 'Hello World'!

If you can't wait to see the result, feel free to check out the full code on this [github repository](https://github.com/DylanMeeus/springboot_jwt_blog).


## Setting up the project
### Getting an application
There are various ways to get started with a Spring Boot project. They have provided a [quick start](http://start.spring.io/) and a CLI-tool. Apart from using the quick start or the Spring Boot CLI, we could also set up a project with Spring Boot manually using either Maven or Gradle and adding the necessary dependencies ourselves. In this post we will set up a project using the provided quick start, which can be found at [start.spring.io](http://start.spring.io/).

When we head over to the quick start webpage, we are presented with a few options on the main page. We will generate a *maven project* with the latest version of Spring Boot (1.4.0 at the time of writing). With the Project Metadata we can set up the standard properties of a maven artifact, but we can just leave it at the default for now.

Next, we can Search for dependencies. Searching for dependencies is a handy feature once you have used Spring Boot several times and are aware of the available dependencies. Because this is our first project however, we can click on the link at the bottom that says *switch to full version*. After doing this, we have a webpage with checkboxes for all the available dependencies. Because we want to create a web-project, we can tick that checkbox under the *web* heading.

There are a lot of dependencies available which work with Spring Boot out-of-the-box. Many of the common technologies are provided here, such as dependencies for HATEOAS, JPA, MongoDB, Thymeleaf and many many more. If we are going to use one of the common Java technologies, chances are that it can easily be included in our Spring Boot project.

For now, all we really need is the *Web* dependency, which gives us several things including a Tomcat server and the Spring MVC framework. Once we click on the *Generate Project* button, a download will start containing a starting project for our selected setup. Setting up a Spring Boot project really is just a matter of minutes!


{% include tweet_quote.html quote_text="Setting up a Spring Boot project is a matter of minutes!" %}

## Our first application

Once we have downloaded our zip file, the project can be imported in our favourite editor. At this point, some code is already generated for us. Let's take a look at some of the code first to understand what Spring Boot has prepared for us.

As this is a maven project, we will examine the `pom.xml` file first. The top of the file is pretty much a standard pom file. It identifies our project by the `groupId` and the `artifactId`. Our project also has a name and a description. Next, there is a reference to a parent, this is the parent of all Spring Boot starters, and contains further dependencies needed for the base of Spring Boot.

This is followed by the properties, which tells us something more about the project. Because we have chosen java 8, the properties also reflect this in the `java.version` tag.

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

The dependencies presented here are the ones that where selected during the setup. Because we only selected the Web dependency, we will see both Web and Test (_Test_ is provided by default, for unit testing our application). Though it might look like there are not a lot of dependencies being pulled in, that's not quite the case. The `spring-boot-starter-...` dependencies that we enter here actually fetch other dependencies they need. Because this is invisible to us, the pom.xml file looks quite clean, and all the management of those dependencies is taken care of for us as well.

Next, there is one plugin, the `spring-boot-maven-plugin`. This will let us build and deploy the entire application with one simple maven command:`mvn spring-boot:run`. If we try to run this now, there won't be anything interesting yet. After running that command, we can go to `localhost:8080`, though all we will see is a "Whitelabel Error Page". But, if all went well, the compilation worked without any problems and our server is up and running. Time to start doing something interesting with it!

## Creating a web application

In this part, we will set up a small application, which will accept HTTP-requests on various paths and return data. To keep this example concise, all the information will be statically provided. At first, we will just expose all the data to everyone, next we will secure some routes with a JWT and provide a login mechanism.

For starters, we will just create a mapping against the root (`/`) of our web-serve in order to verify that this is working, and afterwards we can add more specific routes for the various functions that we will offer. When we want to make methods for certain paths, we need to create a `RestController`. For now we will use our only class for this, we will add the `RestController` and `EnableAutoConfiguration` annotations to this class. The class should be called `DemoApplication` and it is the only class with a main method.


     // package & imports
     
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

When we run our application, again by using `mvn spring-boot:run`, we can navigate to `localhost:8080`, and are presented with a "Hello World" message. (alternatively to using our browser to test the application, we could use a CLI-tool like `curl` or the `postman` application to send requests to the webserver. When dealing with JWTs, [postman](https://www.getpostman.com/) offers an incredibly convenient way of testing our application)


Now we will extend this example a little bit. We will create a new class `UserController` which will be once again annotated with `@RestController`. We will return some (static) JSON data as an example at route `/users` to begin with.

     // package & imports
     
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

### Adding secure routes

The first step is to secure some routes of our application. For this demo we will expose the routes `/` and `/login` to everyone, and only expose `/users` to people whom can provide a valid JWT token. To achieve this, we can start by adding `spring-boot-starter-security` to our `pom.xml`. This will give us the necessary classes to start working with authentication in Spring Boot. In addition, we will add a dependency to manage our JWTs. 

     		<dependency>
     			<groupId>org.springframework.boot</groupId>
     			<artifactId>spring-boot-starter-security</artifactId>
     		</dependency>
     
     		<dependency>
     			<groupId>io.jsonwebtoken</groupId>
     			<artifactId>jjwt</artifactId>
     			<version>0.6.0</version>
     		</dependency>
     		
Because of Spring Boot, we don't need to worry about any other dependencies, they will be taken care of for us. The structure of our application is important here, as Spring needs to be aware of the classes in our project, we can keep the class containing our `main` method at the root. The structure should look like this (don't worry about the classes we did not discuss yet, we'll get to them soon enough). We could deviate from this structure, but then we might need to provide Spring Boot with a way to recognize which classes it should take into account, by using the `@ComponentScan` annotation. 

      - src
         - demo
            - controller
                - UserController.java
            - security
                - jwt
                    - AccountCredentials.java
                    - AuthenticatedUser.java
                    - JWTAuthenticationFilter.java
                    - JWTLoginFilter.java
                    - TokenAuthenticationService
                - WebSecurityConfig.java
            - DemoApplication.java

Once we have set up this structure and changed our pom.xml file, we are ready to start securing our routes. First of all, we want to avoid exposing `/users` to everyone, so we will create a configuration that forbids this. This is done with a Java file as well, using classes provided by the `spring-boot-starter-security` dependency. 

     @Configuration
     @EnableWebSecurity
     public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
         
         @Override
         protected void configure(HttpSecurity http) throws Exception {
             // disable caching
             http.headers().cacheControl();
     
             http.csrf().disable() // disable csrf for our requests.
             .authorizeRequests()
             .antMatchers("/").permitAll()
             .antMatchers(HttpMethod.POST,"/login").permitAll()
             .anyRequest().authenticated()
             .and()
             // We filter the api/login requests
             .addFilterBefore(new JWTLoginFilter("/login", authenticationManager()), UsernamePasswordAuthenticationFilter.class)
             // And filter other requests to check the presence of JWT in header
             .addFilterBefore(new JWTAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
         }
         
Here we have decided that everyone can access `/`, and the `/login` route using `POST`. For all other routes, authentication is required. By adding a filter for both the `/login` route as well as every other route, we can decide what should happen when someone accesses those routes. In this file, we will also add a default account that we can use to test our application.

         @Override
         protected void configure(AuthenticationManagerBuilder auth) throws Exception{
             // Create a default account
             auth.inMemoryAuthentication()
                     .withUser("admin")
                     .password("password")
                     .roles("ADMIN");
         }
         
The great thing here is that we have now secured our application, without having to change code for existing routes. We did not alter our previously created `UserController`, nor did we have to write any xml-configuration. 

At this point, we have some missing classes. Our JWTLoginFilter and JWTAuthenticationFilter still need to be created. They will deal with logging in a user and authenticating one respectively. Before we can use these classes however, we need to create a class that can handle JWTs. 

### Creating a JWT Service

Our JWT Service will deal with the creation and verification of our tokens. In this example, we will create a token based on a username, an expiration time and then sign it with a secret (using an HMAC). We will use `io.jsonwebtoken.Jwts` here for creating and verifying our tokens, they also provide a bunch of algorithms we can use to sign our secret. 
    
     public class TokenAuthenticationService {
     
         private long EXPIRATIONTIME = 1000 * 60 * 60 * 24 * 10; // 10 days
         private String secret = "ThisIsASecret";
         private String tokenPrefix = "Bearer";
         private String headerString = "Authorization";
         public void addAuthentication(HttpServletResponse response, String username)
         {
             // We generate a token now.
             String JWT = Jwts.builder()
                         .setSubject(username)
                         .setExpiration(new Date(System.currentTimeMillis() + EXPIRATIONTIME))
                         .signWith(SignatureAlgorithm.HS512, secret)
                         .compact();
             response.addHeader(headerString,tokenPrefix + " "+ JWT);
         }
     
         public Authentication getAuthentication(HttpServletRequest request)
         {
             String token = request.getHeader(headerString);
             if(token != null)
             {
                 // parse the token.
                 String username = Jwts.parser()
                             .setSigningKey(secret)
                             .parseClaimsJws(token)
                             .getBody()
                             .getSubject();
                 if(username != null) // we managed to retrieve a user
                 {
                     return new AuthenticatedUser(username);
                 }
             }
             return null;
         }
     }

### Adding JWTs to our authentication process

We now have everything set up to use JWTs in our authentication process, recall that we have referenced a `JWTLoginFilter` and `JWTAuthenticationFilter` in our `WebSecurityConfig`. We'll first take a look at our `JWTLoginFilter`, which will intercept `POST` requests on the `/login` path, and attempt to authenticate our user. When our user is successfully authenticated, we will return a JWT in the `Authorization` header of the response. 

In the constructor of our filter, we will reference the parent class and specify on which url this filter should act. 

     public class JWTLoginFilter extends AbstractAuthenticationProcessingFilter{
     
         private TokenAuthenticationService tokenAuthenticationService;
     
         public JWTLoginFilter(String url, AuthenticationManager authenticationManager)
         {
             super(new AntPathRequestMatcher(url));
             setAuthenticationManager(authenticationManager);
             tokenAuthenticationService = new TokenAuthenticationService();
         }
         
We have to override the method for attempted authentications and successful authentications. During the authentication attempt, we will retrieve the username and password from the request. After they are retrieved, we will use the `AuthenticationManager` to verify that these details match with an existing user. If this is the case, we will enter the method `successfulAuthentication`. Here we will fetch the name from the authenticated user, and pass this on to our `TokenAuthenticationService`, which will then add a JWT to the response. 
     
         @Override
         public Authentication attemptAuthentication(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse)
                 throws AuthenticationException, IOException, ServletException {
             AccountCredentials credentials = new ObjectMapper().readValue(httpServletRequest.getInputStream(),AccountCredentials.class);
             UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken(credentials.getUsername(), credentials.getPassword());
             return getAuthenticationManager().authenticate(token);
         }
     
         @Override
         protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication)
                 throws IOException, ServletException{
             String name = authentication.getName();
             tokenAuthenticationService.addAuthentication(response,name);
         }
     }

One extra class that we need is the `AccountCredentials` class. This will be used to map our request data when a `POST` call is made to our `/login` path`, it should contain the username and password as part of the body. This is a simple _POJO_ which should just contain the fields that we want to retrieve from the request, along with getters and setters. 

     public class AccountCredentials {
     
         private String username;
         private String password;
     
         // getters + setters
     }

### Running our application

Our application is now secured and supports authentication with a JWT, we can go ahead and run our application again. When our server is up-and-running we can test this out by querying `localhost:8080/users`, and we should get a message saying "Access Denied". To authenticate ourselves, we will send a POST-request to `/login` with our user's data in the body: `{"username":"admin","password":"password"}`. 

In the response of this request, we will get a token as part of the _Authorization_ header, prefixed by "Bearer". We can copy this token to issue the next GET request to our `/users` path. To do this, alter the request to `/users` to include a header called `Authorization`, paste the JWT in this field and launch the request! If all went well, we should once again be presented with the users. 


## Advantages of using Spring Boot

After this we have completed a Spring Boot application, let's reflect on the advantages this gave us:

** Fast development: **
Setting up the project took us just a few minutes, getting it to run just a few more. 

** Many dependencies managed for us: **
Whilst we still had to alter our pom.xml file, we did not have to include a lot of new dependencies. Many of the dependencies we needed where added by just two spring-boot dependencies. Issues of incompatible dependencies are largely gone because of this (we did include one dependency ourselves). 

** No XML-configuration: **
At no point did we have to write XML-configuration files, all configuration was done from within Java files by the use of annotations and existing classes and methods. 

** Self-contained applications: **
The applications that we can make with Spring Boot are self-contained. We can simply run the application using one simple command, and the deployment (containing a webserver) is done for us. 

** Leverage a mature framework: **
Spring Boot can leverage the power of the mature Spring framework, which is something many Java developers are already familiar with, making adoption of Spring Boot more convenient!

** Well documented: **
Being well documented is important, and Pivotal has great documentation on how to get started with Spring Boot and Spring. If you're curious about learning more, you should check out the [Getting Started Guides](https://spring.io/guides).  


### Spring Boot and MicroServices

One of the advantages of Spring Boot lies with microservices. Microservices are a successor of the SOA, service oriented architecture. A microservice is responsible for managing a single data domain and the functions thereof. Spring Boot is actually a great example of this principle, as it consists of many different modules that we can use. Recall that during the setup of our application, we could choose between a whole range of these dependencies, dependencies which would be added to our project by including a simple reference to them in our pom.xml file. Each of these dependencies can be thought of as a microservice. Each project would function as a self-containing service that we can then use in other projects. 

In our example, we have created a REST-service that is self-contained. We could focus ourselves on writing just the REST-api that deals with logging in and retrieving a list of users. We could then create another Spring Boot project for another part of our application (say, for example, a JSP application or desktop client), if we would be so inclined to do. These Spring Boot applications could then communicate with each other via HTTP, but would be largely independent of each other. This all makes Spring Boot a popular choice in modern architectures, and one worth checking out!


## Conclusion

Creating a project with Spring Boot makes development a lot easier. We can save a lot of time otherwise spend on dealing with configuration and deployment that are now taken care of for us. Adding a great deal of functionality to our project can be done by including another dependency in our pom.xml, and all this gets bundled into one self-contained, easily-deployed application. As you can tell from the start of this project, there is a lot more to explore with Spring Boot, [so what are you waiting for? ;-)](http://start.spring.io/)



