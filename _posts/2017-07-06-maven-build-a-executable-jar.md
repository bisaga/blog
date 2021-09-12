---
layout: post
title: "Maven:  Build a executable JAR"
date: "2017-07-06"
categories: 
  - "java"
tags: 
  - "spring-boot"
---

# Prepare java deploy package

In true microservice architecture we need to prepare self sufficient applications which must be very easy to deploy. For java server app this means executable JAR file with all dependencies included.

Because I use spring boot application my application already is self sufficient. I only need to prepare a combined JAR with all dependencies.

## Maven

In the maven pom.xml file I add build plugin:

<build>
    <plugins>
...
         <plugin>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-maven-plugin</artifactId>
         </plugin>
...
    </plugins>
</build>

After executing a maven "**package**" lifecycle command, maven will produce combined JAR with everything inside in the target folder :

\[INFO\] 
\[INFO\] --- maven-jar-plugin:2.6:jar (default-jar) @ myapp ---
\[INFO\] Building jar: C:\\Bisaga\\Workspaces\\myapp\\server\\SpringBootMyApp\\target\\myapp-0.0.1-SNAPSHOT.jar
\[INFO\]

Now I can run whole server app with simple jar command:

$ java -jar target/myapp-0.0.1-SNAPSHOT.jar

Because this executable jar include everything, web server, drivers, configurations etc, it will immediately serve REST services to the world.

Such an application could be deployed to any virtual machine instance locally or on the cloud.

##### You can get [source code](https://github.com/bisaga/SpringBootMyApp) of this project from github repository "[SpringBootMyApp](https://github.com/bisaga/SpringBootMyApp)" .
