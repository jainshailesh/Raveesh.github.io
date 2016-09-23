---
layout: post
title: "Spring Boot quickGuide"
date: 2016-09-23
---

# [Spring Boot Guide](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/)

### Installation
1) You can add the spring boot parent project or spring boot maven dependency
```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot</artifactId>
			<version>${spring.version}</version>
		</dependency>
```

2) To build a fat jar use the following maven buld configuration
```
<build>
		<finalName>deviceManager</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<version>1.4.0.RELEASE</version>
				<executions>
					<execution>
						<goals>
							<goal>repackage</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
```

### Creating a Spring Boot web application

Example : 
```
@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Example.class, args);
    }

}
```
#### Important Annotations
1.  *@RestController*. This is known as a stereotype annotation and provides hints to people / spring that it should be considered
for handling requests. Important: Use @Controller with @RequestMapping for routing. 
2. *@EnableAutoConfiguration* : makes spring guess based on the jars configured in maven dependency. If spring-boot-starter-web is 
present in pom, then spring will assume you are building a web application. 
3. *main method* : SpringApplication will bootstrap our application, starting Spring which will in turn start the 
auto-configured Tomcat web server.

### Running the spring Application 
1. Use ``` mvn spring-boot:run ```
2. It is possible to get Spring Boot to work with other build systems (Ant for example), but they will not be particularly well supported.

### Spring Boot Starters
1. All official starters follow a similar naming pattern; spring-boot-starter-*, where * is a particular type of application. 
This naming structure is intended to help when you need to find a starter. 
Look at [link](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/) for all starter projects
2. There are 2 spring production starter projects: 
    1. spring-boot-starter-actuator
    2. spring-boot-starter-remote-shell

### Code Structuring
1. Never use the default package
2. Main application class must be in  root package above other classes to help @EnableAutoConfiguration to scan through classes.
3. You need to opt-in to auto-configuration by adding the @EnableAutoConfiguration or @SpringBootApplication annotations to one of your
@Configuration classes.
4. You can exclude classes from autowiring by  @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
5. The @SpringBootApplication annotation is equivalent to using @Configuration, @EnableAutoConfiguration and @ComponentScan with their 
default attributes

### Startup Failures
1. Registered FailureAnalyzers get a chance to provide a dedicated error message and a concrete action to fix the problem. 
For instance if you start a web application on port 8080 and that port is already in use



### Miscellaneous
1. Spring Boot use caches to improve performance. For example, Thymeleaf will cache templates to save repeatedly parsing XML source files.
2. The banner that is printed on start up can be changed by adding a banner.txt file to your classpath, or by setting banner.location 
to the location of such a file.The SpringApplication.setBanner(…​) method can be used if you want to generate a banner 
programmatically. Use the org.springframework.boot.Banner interface and implement your own printBanner() method.
3. Some events are actually triggered before the ApplicationContext is created so you cannot register a listener on those as a @Bean.
You can register them via the SpringApplication.addListeners(…​) or SpringApplicationBuilder.listeners(…​) 
methods.If you want those listeners to be registered automatically regardless of the way the application is created you can 
add a META-INF/spring.factories file to your project and reference your listener(s) using the 
org.springframework.context.ApplicationListener key.
```
org.springframework.context.ApplicationListener=com.example.project.MyListener
```
