---
layout: post
title: "Web compression on Spring Boot application"
date: "2017-07-01"
categories: 
  - "java"
  - "programming"
tags: 
  - "compression"
  - "gzip"
  - "spring-boot"
---

# Compression of JSON messages

If you look at common JSON message, the data are highly repetitive ! Each array of objects contains meta data for all object properties. If all of your messages are short you probably won't need a compression but in typical scenario where REST services return data from database you gain a lot.

[![](/assets/images/2017-07-01-07_48_44-Postman-300x183.png)](http://bisaga.com/blog/wp-content/uploads/2017/07/2017-07-01-07_48_44-Postman.png)In the message of this size (220 bytes) there is no gain at all (compressed size is 250 bytes) and you lost some processor time to compress it. It is total waste of resources, so you have to limit compression above 2 kb for example.

## Lets find out how much difference we make with a compression in the real world example

For the test we will get JSON message from publicly accessible site **http://data.colorado.gov/resource/4ykn-tg5h.json** and check the message size. The size of received message in uncompressed JSON format is  **951 kb**.

[![](/assets/images/2017-07-01-21_19_44-Windows-Shell-Experience-Host-266x300.png)](http://bisaga.com/blog/wp-content/uploads/2017/07/2017-07-01-21_19_44-Windows-Shell-Experience-Host.png)

Now I will check in network sniffer how big this message really was : [![](/assets/images/2017-07-01-21_15_28-Windows-Shell-Experience-Host-300x193.png)](http://bisaga.com/blog/wp-content/uploads/2017/07/2017-07-01-21_15_28-Windows-Shell-Experience-Host.png)The message on the TCP level is only **170 kb** ! If you look at the message payload (down right), what was really on the wire is unreadable compressed data. You can get more information [here](http://www.colasoft.com/capsa-free/) about [Capsa network analyzer](http://www.colasoft.com/knowledge-base/step-by-step) freeware product used in this measurement.

It means that this type of messages were compressed to less then 20% of the original size. It means at least **5 times smaller** network traffic as without compression.  The compression ratio could be even higher, much higher, if your specific messages are not so full of unique data and that's happening a lot in ERP type applications.

## Enable compression in Spring boot application

Add this settings to [application.properties](https://github.com/bisaga/SpringBootMyApp/blob/master/src/main/resources/application.properties) file:

\# Server compression
server.compression.enabled=true
server.compression.min-response-size=2048
server.compression.mime-types=application/json,application/xml,text/html,text/xml,text/plain

## How to check if compression is enabled

Check for a specific statement about content encoding in the response header :

[![](/assets/images/2017-07-01-08_18_30-Postman-300x218.png)](http://bisaga.com/blog/wp-content/uploads/2017/07/2017-07-01-08_18_30-Postman.png)

To inspect messages in JSON format I use [Postman API development environment, free edition](https://www.getpostman.com/postman).

This is the link to the source code of the used [project on the github](https://github.com/bisaga/SpringBootMyApp).
