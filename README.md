# Building an Web Application with Spring Boot

1-Make a **Gradle** Project by default language syntax with IDE

2- Make Project's package structure(**ir.seefa.sample.project.web**) in _src/main/java_ and _src/test/java_

* Hint: Be careful about word spelling for packages because Test classes will not able to run SpringBoot Test Runner if *Packages* or *Classes* are not match names.

3- add below codes to **build.gradle** or **build.gradle.kts** to get **Spring Boot** dependencies and project features(Project name, Project version, Java plugin, dependencies, Add a task to making JAR file, Java configuration and so on)

```
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.1.6.RELEASE'
}

apply plugin: 'java'
apply plugin: 'io.spring.dependency-management'

group 'ir.seefa.sample.project'
version '1.0-SNAPSHOT'

sourceCompatibility = '11'
targetCompatibility = '11'


bootJar {
    baseName = 'spring-boot-web-starter'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation group: 'org.springframework.boot', name: 'spring-boot-starter-web'
    testImplementation group: 'org.springframework.boot', name: 'spring-boot-starter-test'
    compile group: 'org.springframework.boot', name: 'spring-boot-starter-actuator'
    testCompile group: 'junit', name: 'junit'
}

```
* Tips: 
    1) plugins -> _java_, _org.springframework.boot_
    2) apply plugin -> _io.spring.dependency-management_
    3) Project properties: _Group_,_Version_,_SourceCompatibility_
    4) repositories
    5) dependencies implementation -> _spring-boot-starter-web_, testImplementation -> _spring-boot-starter-test_, compile -> _spring-boot-starter-actuator_, testCompile -> _junit_

4- Add **Application** class to has been made package from Step 2 with following code:

* Tips:
    1) Spring Boot "io.spring.dependency-management" plugin automatically will find any main class "public static void main(String[] args)" to run Application
    2) commandLineRunner method used to print all provided beans by Spring Boot.
    
```
package ir.seefa.sample.project.web;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;

import java.util.Arrays;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
        return args -> {

            System.out.println("Let's inspect the beans provided by Spring Boot:");

            String[] beanNames = ctx.getBeanDefinitionNames();
            Arrays.sort(beanNames);
            for (String beanName : beanNames) {
                System.out.println(beanName);
            }

        };
    }

}

```
5- Add **HelloController** class to handle "/" Http request as Rest Controller and return a clear test as output. add following codes:

```
package ir.seefa.sample.project.web;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/")
    public String index() {
        return "Greetings from Spring Boot!";
    }
}

```

6- Add **application.properties** file to _src/main/resources_ path for apply application configuration before run such server port as follows:

```
server.port=9090
management.endpoint.shutdown.enabled=true
```

7- Add **HelloControllerTest** class to _/src/test/java/[Package_Name]_ with following codes to test Application controller:

```
package ir.seefa.sample.project.web;

import static org.hamcrest.Matchers.equalTo;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class HelloControllerTest {
    @Autowired
    private MockMvc mvc;

    @Test
    public void getHello() throws Exception {
        mvc.perform(MockMvcRequestBuilders.get("/").accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string(equalTo("Greetings from Spring Boot!")));
    }
}

```

8- Another test class **HelloControllerIT** used to run standalone version of Application and run unit tests. Its code is as follows:

```
package ir.seefa.sample.project.web;

import static org.hamcrest.Matchers.*;
import static org.junit.Assert.*;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.net.URL;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloControllerIT {

    @LocalServerPort
    private int port;

    private URL base;

    @Autowired
    private TestRestTemplate template;

    @Before
    public void setUp() throws Exception {
        this.base = new URL("http://localhost:" + port + "/");
    }

    @Test
    public void getHello() throws Exception {
        ResponseEntity<String> response = template.getForEntity(base.toString(),
                String.class);
        assertThat(response.getBody(), equalTo("Greetings from Spring Boot!"));
    }

}

```

9- run command **gradle** in command line

10- run command **gradle tasks** in command line

11- Build your project with Gradle Wrapper => Run in command line(gradle wrapper --gradle-version 5.4.1)

12- After passing step No.11, we can only run this command to build project(**./gradlew build**)

13- Run this command will execute our Application(**./gradlew run**)  or Run **Application** directly.

14- Another way to run application is run below command in terminal:

```
./gradlew build && java -jar build/libs/gs-spring-boot-0.1.0.jar
```

Tips: below lines will be show in Console during run application by SpringBoot Command Line Runner bean:

```
Let's inspect the beans provided by Spring Boot:
application
beanNameHandlerMapping
defaultServletHandlerMapping
dispatcherServlet
embeddedServletContainerCustomizerBeanPostProcessor
handlerExceptionResolver
helloController
httpRequestHandlerAdapter
messageSource
mvcContentNegotiationManager
mvcConversionService
mvcValidator
org.springframework.boot.autoconfigure.MessageSourceAutoConfiguration
org.springframework.boot.autoconfigure.PropertyPlaceholderAutoConfiguration
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration$DispatcherServletConfiguration
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration$EmbeddedTomcat
org.springframework.boot.autoconfigure.web.ServerPropertiesAutoConfiguration
org.springframework.boot.context.embedded.properties.ServerProperties
org.springframework.context.annotation.ConfigurationClassPostProcessor.enhancedConfigurationProcessor
org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.web.servlet.config.annotation.DelegatingWebMvcConfiguration
propertySourcesBinder
propertySourcesPlaceholderConfigurer
requestMappingHandlerAdapter
requestMappingHandlerMapping
resourceHandlerMapping
simpleControllerHandlerAdapter
tomcatEmbeddedServletContainerFactory
viewControllerHandlerMapping
```

15- after running Application we can test it by Explorer or curl command as follows:

```
$ curl localhost:8080
Greetings from Spring Boot!
```

16- Last step is checking application health with Actuator remote application monitoring tools by enabling it with its dependency in *build.gradle* file.

use following command in terminal to check Application activity:

```
$ curl localhost:8080/actuator/health
{"status":"UP"}
```

or after enabling **management.endpoint.shutdown.enabled** property in **application.properties** configuration file we can shutdown application with remote command[Of course it dose not work for me :( ]

```
$ curl -X POST localhost:8080/actuator/shutdown
{"timestamp":1401820343710,"error":"Method Not Allowed","status":405,"message":"Request method 'POST' not supported"}
```

