---
title: "Angular environment with java backend server app setup"
date: "2017-05-09"
categories: 
  - "angular"
  - "javascript"
  - "programming"
  - "typescript"
---

# Install Angular toolchain

Before you can start working with angular you need to install [nodejs](https://nodejs.org/) and [npm](https://docs.npmjs.com/getting-started/installing-node). Your angular development will mostly rely on those two tools.

## [Typescript](https://www.npmjs.com/package/typescript)

You need to install typescript, in which your you will write code.

$ npm install -g typescript

## [Angular.io](https://angular.io/)

Angular is a tool for building web & desktop oriented multi-platform client applications. Angular comprise of all development tools to build fully featured web application. To build standalone desktop applications with native functionalities running on Windows, iOS or  Linux you can integrate your angular app with [Electron platform](https://electron.atom.io/).

## [AngularCLI](https://cli.angular.io/)

After initial installation of npm you install angular cli (command line interface) to speed up development.

$ npm install -g @angular/cli@latest

###### Update CLI tools to latest version

You need to uninstall previous version and install latest version again.

$ npm uninstall -g @angular/cli 
$ npm cache clean
$ npm install -g @angular/cli@latest 
$ ng -v

On the end you can check installed version with "ng -v" command.

## [Visual studio code](https://code.visualstudio.com/)

Best code editor & debugger for angular/typescript/javascript.

### Create new angular application scaffold

If you create client side and server side application it is wise to create single parent folder for both part of application and divide it with two folders (server and client for example).

To create new application structure for angular web application, go to "client" application folder and create new application:

$ ng new app\_name

To generate new application with build in support for SCSS CSS styles support:

$ ng new app\_name --style=scss

### To change existing app to support SCSS styles:

$ ng set defaults.styleExt=scss

### Serve application in development

Application will be compiled and local node server will serve it at http://localhost:4200 address.

$ ng serve

## Java Spring boot server app and Angular client app setup

If you develop **Java spring boot** server application to serve JSON services on it, on embedded tomcat server for example, and running it at address localhost:8080,  but your client app is served on localhost:4200 address, than you will need some sort of proxy to overcome cross site request forgery limitations. In that case you [define proxy](http://stackoverflow.com/questions/37172928/angular-cli-server-how-to-proxy-api-requests-to-another-server) on your angular client application. That way all your requests from angular application to the server addresses beginning with  localhost:4200**/api** suffix will be redirected to tomcat server running on 8080 port.

##### Setup proxy address

Create **proxy-conf.json** file like this (there is only one path redirection definition "**/api**" ) :

{
    "/api": {
        "target": "http://localhost:8080",
        "secure": false
    }
}

In your package.json file define new **start** command for **npm**:

...
"scripts": {
    "ng": "ng",
    "start": "ng serve --proxy-config proxy-conf.json",

...

#### Startup angular client app

Don't forget to use "**npm start**" instead of "**ng serve**" to run angular application that way.

$ npm start

Now you can start debugging  process on your java server app directly from your angular client app.

For example your java service is named "greeting" and you publish it under "http://localhost:8080/api/greeting" path. When you load it from angular web page your address will be "http://localhost:4200/api/greeting" and because you use proxy redirection your request will arrive on tomcat server.

#### How to run arbitrary command with "npm"

To run an arbitrary command from package's scripts object use "npm run".

$npm run "command\_from\_scripts\_section"

This is useful if you use IDE with terminal command window, you can run REST server directly from your angular project folder for example.

Add "java -jar project.jar" command to package.json:

  "scripts": {
    "ng": "ng",
    "start": "ng serve --proxy-config proxy-conf.json",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e",
    "server": "java -jar ../../server/SpringBootMyApp/target/myapp-0.0.1-SNAPSHOT.jar"
  },

Now if you run "server" command with npm run, you will start "java -jar ..." command:

$ npm run server

[Here](http://bisaga.com/blog/java/maven-build-a-executable-jar/) I describe how to prepare your java spring boot project as executable jar.
