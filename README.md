# Spring boot application with docker plugin.

This is pure application generated with spring boot initializer(https://start.spring.io/).

Add docker plugin detail to your build.gradle
```gradle
buildscript {
	ext {
		springBootVersion = '1.5.3.RELEASE'
	}
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
		classpath 'se.transmode.gradle:gradle-docker:1.2'
	}
}

apply plugin: 'java'
apply plugin: 'eclipse'

apply plugin: 'org.springframework.boot'
apply plugin: 'docker'

version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
	mavenCentral()
}


dependencies {
	compile('org.springframework.boot:spring-boot-starter-jersey')
	compile('org.springframework.boot:spring-boot-starter-thymeleaf')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}

task buildDocker(type: Docker) {
	baseImage = 'develar/java:latest'
	push = project.hasProperty('push')
	tag = 'simple-app-docker-plugin'
	addFile {
		from jar
		rename {'simple-app-docker-plugin.jar'}

}
	entryPoint(['java', '-Djava.security.egd=file:/dev/./urandom', '-jar', '/simple-app-docker-plugin.jar'])
	exposePort(8090) //port used by application
}

buildDocker.dependsOn(build)
```
To create Docker file and push image to docker run 
```
gradlew clean
gradlew build
gradlew buildDocker 
```
Remember to have docker up and running, if everything is Ok you can see logs in docker build tasks:
```
:buildDocker
      [tar] Building tar: C:\opt\spring-boot-examples\simple-app-docker-plugin\build\docker\add_1.tar

Step 1/3 : FROM develar/java:latest
 ---> edc8788ce767
Step 2/3 : ADD add_1.tar /
 ---> 2fda2b42e1d6
Removing intermediate container 20193dcadd16
Step 3/3 : ENTRYPOINT java -Djava.security.egd=file:/dev/./urandom -jar /simple-app-docker-plugin.jar
 ---> Running in 547cf284d088
 ---> e74e5fa15b94
Removing intermediate container 547cf284d088
Successfully built e74e5fa15b94
```

To chekc if our image is build and push to docker run command:
```
docker images
```
output:
```
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
simple-app-docker-plugin   0.0.1-SNAPSHOT      e74e5fa15b94        17 minutes ago      144 MB
develar/java               latest              edc8788ce767        12 months ago       118 MB

```
You can check your dokcer file in build/docker/Dockerfile

```
FROM develar/java:latest
ADD add_1.tar /
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/simple-app-docker-plugin.jar"]
```
To run our new image in docker run command :
```
docker run simple-app-docker-plugin:0.0.1-SNAPSHOT
```
output:
```springboot

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.3.RELEASE)

2017-06-04 17:46:50.658  INFO 1 --- [           main] c.p.s.SimpleAppDockerPluginApplication   : Starting SimpleAppDockerPluginApplication on 32db86805a40 with PID 1 (/simple-app-docker-plugin.jar started by root in /)
2017-06-04 17:46:50.664  INFO 1 --- [           main] c.p.s.SimpleAppDockerPluginApplication   : No active profile set, falling back to default profiles: default
2017-06-04 17:46:51.130  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@4b9af9a9: startup date [Sun Jun 04 17:46:51 GMT 2017]; root of context hierarchy
2017-06-04 17:46:52.329  INFO 1 --- [           main] f.a.AutowiredAnnotationBeanPostProcessor : JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
2017-06-04 17:46:52.928  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2017-06-04 17:46:52.954  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service Tomcat
2017-06-04 17:46:52.956  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.14
2017-06-04 17:46:53.079  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2017-06-04 17:46:53.079  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1953 ms
2017-06-04 17:46:53.265  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Mapping servlet: 'dispatcherServlet' to [/]
2017-06-04 17:46:53.270  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2017-06-04 17:46:53.270  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2017-06-04 17:46:53.270  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2017-06-04 17:46:53.271  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2017-06-04 17:46:53.637  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@4b9af9a9: startup date [Sun Jun 04 17:46:51 GMT 2017]; root of context hierarchy
2017-06-04 17:46:53.738  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2017-06-04 17:46:53.739  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2017-06-04 17:46:53.770  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-06-04 17:46:53.770  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-06-04 17:46:53.808  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-06-04 17:46:54.351  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-06-04 17:46:54.431  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2017-06-04 17:46:54.439  INFO 1 --- [           main] c.p.s.SimpleAppDockerPluginApplication   : Started SimpleAppDockerPluginApplication in 4.401 seconds (JVM running for 4.91)
```

to push image to docker registy installed on remote host,change line with tag to :

```gradle
task buildDocker(type: Docker) {
	baseImage = 'develar/java:latest'
	push = project.hasProperty('push')
	*tag = '10.32.15.221:5000/simple-app-docker-plugin'*
	addFile {
		from jar
		rename {'simple-app-docker-plugin.jar'}

}
	entryPoint(['java', '-Djava.security.egd=file:/dev/./urandom', '-jar', '/simple-app-docker-plugin.jar'])
	exposePort(8090) //port used by application
}
```

and run
```
gradlew dockerBuild -Ppush

```







