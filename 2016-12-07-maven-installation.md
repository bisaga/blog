---
title: "Maven - installation"
date: "2016-12-07"
categories: 
  - "java"
  - "programming"
tags: 
  - "maven"
---

# How to install Maven on Windows 10

1. Download [Apache Maven ZIP](http://maven.apache.org/download.cgi) file and unzip it to some folder (example: C:\\Programs\\apache-maven-3.3.9 ).
2. Add maven folder to environment variables:

- MAVEN\_HOME = C:\\Programs\\apache-maven-3.3.9
- M2\_HOME = %MAVEN\_HOME%
- add to path = %MAVEN\_HOME%\\bin

Test:

$ mvn --version

$ mvn --version
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T17:41:47+01:00)
Maven home: C:\\Programs\\apache-maven-3.3.9
Java version: 1.8.0\_101, vendor: Oracle Corporation
Java home: C:\\Program Files\\Java\\jdk1.8.0\_101\\jre
Default locale: en\_US, platform encoding: Cp1252
OS name: "windows 10", version: "10.0", arch: "amd64", family: "dos"
