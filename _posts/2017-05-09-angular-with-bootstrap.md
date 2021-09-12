---
layout: post
title: "Angular with Bootstrap"
date: "2017-05-09"
categories: 
  - "angular"
  - "javascript"
  - "programming"
  - "typescript"
---

# How to add [Bootstrap](http://getbootstrap.com/) to your angular application

Add dependency to your package.json file, this will install bootstrap to your local node\_modules folder with "**npm update**" command.

"dependencies" : {
...
    "bootstrap": "^3.3.7",
    "font-awesome": "^4.7.0"
}, 

Add CSS files to angular cli file (.angular-cli.json)

"apps": \[{
...
      "styles": \[
        "../node\_modules/bootstrap/dist/css/bootstrap.css",
        "../node\_modules/font-awesome/css/font-awesome.css",
        "styles.css"
      \],
...
}\]

Now you can start using bootstrap classes in HTML templates.

<div class='panel panel-primary'>
  <div class='panel-heading'>
    Title
  </div>
  <div class='panel-body'>
    <div class='row'>
       <div class='col-md-2'>Filter by:</div>
       <div class='col-md-4'>
         <input type='text'/>
       </div>
    </div>

...
