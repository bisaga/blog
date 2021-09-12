---
title: "Spring boot and Netbeans"
date: "2016-12-07"
categories: 
  - "programming"
---

# Setup spring boot application

Simplest way to create "netbeans" ready project is to create project zip file with [spring web wizard](http://start.spring.io) and unpack it somewhere. Open the wizard page and switch to the full version.

[![2016-12-07-22_04_31-spring-initializr](images/2016-12-07-22_04_31-Spring-Initializr-300x244.png)](http://bisaga.com/blog/wp-content/uploads/2016/12/2016-12-07-22_04_31-Spring-Initializr.png)Select all needed dependency, for example "web", "jax-rs", jOOQ etc.

After you click on "Generate project" or press Alt+Return, you will get zip file "save as" dialog. This file is created with all necessary configurations to start developing web application.   Just extract it somewhere and open project folder from netbeans.

Created project is [**maven**](http://bisaga.com/blog/programming/maven-installation/) project with "pom.xml" definition file, you will need [maven installed](http://bisaga.com/blog/programming/maven-installation/) on your system.
