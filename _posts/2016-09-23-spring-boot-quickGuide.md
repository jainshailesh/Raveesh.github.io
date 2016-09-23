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

### Application Events

1. An ApplicationStartedEvent is sent at the start of a run, but before any processing except the registration of listeners and initializers.
2. An ApplicationEnvironmentPreparedEvent is sent when the Environment to be used in the context is known, but before the context is created.
3. An ApplicationPreparedEvent is sent just before the refresh is started, but after bean definitions have been loaded.
4. An ApplicationReadyEvent is sent after the refresh and any related callbacks have been processed to indicate the application is ready to service requests.
5. An ApplicationFailedEvent is sent if there is an exception on startup.

### Using the ApplicationRunner or CommandLineRunner

1. If you need to run some specific code once the SpringApplication has started, you can implement the ApplicationRunner or CommandLineRunner interfaces. Both interfaces work in the same way and offer a single run method which will be called just before SpringApplication.run(…​) completes.
2. If you need to access the application arguments that were passed to SpringApplication.run(…​) you can inject a org.springframework.boot.ApplicationArguments bean. The ApplicationArguments interface provides access to both the raw String[] arguments as well as parsed option and non-option arguments:
3. In addition, beans may implement the org.springframework.boot.ExitCodeGenerator interface if they wish to return a specific exit code when the application ends.

### Spring Properties and Yaml
1. Using the @Value("${property}") annotation to inject configuration properties can sometimes be cumbersome, especially if you are working with multiple properties or your data is hierarchical in nature. Spring Boot provides an alternative method of working with properties that allows strongly typed beans to govern and validate the configuration of your application. We can use the @ConfigurationProperties to inject the properties directly into the bean.
2. YAML files can’t be loaded via the @PropertySource annotation. So in the case that you need to load values that way, you need to use a properties file.
3. YAML is a superset of JSON, and as such is a very convenient format for specifying hierarchical configuration data. The SpringApplication class will automatically support YAML as an alternative to properties whenever you have the SnakeYAML library on your classpath.


### To enable custom logging 
```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
			<version>1.3.7.RELEASE</version>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
```
### Spring MVC
1. Spring MVC uses the HttpMessageConverter interface to convert HTTP requests and responses. Sensible defaults are included out of the box, for example Objects can be automatically converted to JSON (using the Jackson library) or XML (using the Jackson XML extension if available, else using JAXB). 
2. All @JsonComponent beans in the ApplicationContext will be automatically registered with Jackson, and since @JsonComponent is meta-annotated with @Component, the usual component-scanning rules apply.Spring Boot also provides JsonObjectSerializer and JsonObjectDeserializer base classes which provide useful alternatives to the standard Jackson versions when serializing Objects. 
3. Spring MVC has a strategy for generating error codes for rendering error messages from binding errors: MessageCodesResolver. Spring Boot will create one for you if you set the spring.mvc.message-codes-resolver.format property PREFIX_ERROR_CODE or POSTFIX_ERROR_CODE (see the enumeration in DefaultMessageCodesResolver.Format).
4. If you want to display a custom HTML error page for a given status code, you add a file to an /error folder. Error pages can either be static HTML (i.e. added under any of the static resource folders) or built using templates. The name of the file should be the exact status code or a series mask.
5. Actuator endpoints allow you to monitor and interact with your application. Spring Boot includes a number of built-in endpoints and you can also add your own. For example the health endpoint provides basic application health information.
6. 


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
4. It is often desirable to call setWebEnvironment(false) when using SpringApplication within a JUnit test.
5. It is possible to enable admin-related features for the application by specifying the spring.application.admin.enabled property.
