---
layout: post
title: "Angular - Add Bootstrap to your application"
date: "2017-07-06"
categories: 
  - "angular"
  - "css"
  - "programming"
tags: 
  - "angular"
  - "bootstrap"
  - "css"
---

# Bootstrap components / styles installation

In the root of your angular project open **package.json** file and add new dependency for Bootstrap and font-awesome.

...
"dependencies": {
    ...
    ...
    "bootstrap": "^3.3.7",
    "font-awesome": "^4.7.0"
}
...

Update installed dependencies with npm :

$ npm install

Add new directive for styles in **.angular-cli.json** configuration

...
      "styles": \[
        "../node\_modules/bootstrap/dist/css/bootstrap.css",
        "../node\_modules/font-awesome/css/font-awesome.css",
        "styles.css"
      \],
...

After you build your angular application, new webpack prepared application will contain bootstrap components and font.

## Start using bootstrap

Simply use bootstrap class directives in your templates, partial template code:

<div class='panel panel-default'>
  <div class='panel-heading'>
    <h3 class='panel-title'>
      {{title}}
    </h3>
  </div>
...
...

And the result of the example :

[![](/assets/images/2017-07-06-08_36_38-AngularMyApp-300x109.png)](http://bisaga.com/blog/wp-content/uploads/2017/07/2017-07-06-08_36_38-AngularMyApp.png)
