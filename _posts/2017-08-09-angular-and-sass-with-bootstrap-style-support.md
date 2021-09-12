---
layout: post
title: "Angular and Sass with Bootstrap 3 style support"
date: "2017-08-09"
categories: 
  - "angular"
  - "css"
  - "programming"
  - "sass"
  - "scss"
tags: 
  - "angular"
  - "angular-cli"
  - "bootstrap"
  - "css"
  - "sass"
---

# Integrating [Sass](http://sass-lang.com/) and Bootstrap 3 to angular project

If you want to customize default bootstrap 3 CSS theme, you will need to change it. But to be able to do that you will need to install CSS extension language Sass and **bootstrap-sass** definitions and then compile custom bootstrap CSS within your angular application.  This is officially supported by angular-cli tools.

## Setup angular project for Sass

If you create new angular project you can add option "--styles=scss" to "new" command. Yo can also change existing project style settings with "set" command:

$ ng new my\_app --style=scss
or
$ ng set defaults.styleExt scss 

To compile scss files to css files we need node-sass compiler :

npm install node-sass --save-dev

Next we install bootstrap-sass in our project :

npm install bootstrap-sass --save

## Writing CSS with Sass

First we create "scss" subfolder under the "assets" folder and create main.scss file in it.

 src
 !--- assets
      !--- scss
             main.scss

Angular project expect your main.scss file defined in the .angular-cli.json file:

"styles": \[
        "assets/scss/main.scss"
      \],

Start the project and angular-cli will compile SCSS and include result CSS into your application.

## Test Sass compiler and angular integration

Add some simple scss definition to main.scss:

$default-font: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
$default-font-size: 16px;
$default-title-size: 200%;
$default-title-weight: bold;

.mytitle {
   font: $default-title-weight $default-title-size $default-font;
}

Now add a paragraph with "mytitle" class to your index.html :

...
<body>
  <p class="mytitle">This is a test</p>
...

If everything works  as expected you will see a test paragraph with correct font and weight as defined by scss:

[![](/assets/images/2017-08-09-20_52_45-AngularMyApp-300x138.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-09-20_52_45-AngularMyApp.png)

Scss compiler create proper CSS definition and angular with webpack create styles.bundle.js file where we can find our "mytitle" class defined as :

// module
exports.push(\[module.i, ".mytitle {\\n  font: bold 200% \\"Segoe UI\\", Tahoma, Geneva, Verdana, sans-serif; }\\n", ""\]);

## Import Bootstrap Sass definitions

I am changing my existing seed project so you can get [source code](https://github.com/bisaga/SpringBootMyApp) of this project from github repository “[ANGULARMYAPP](https://github.com/bisaga/AngularMyApp)” .

Immediately after changing style definition to SCSS in our project, we lost all bootstrap CSS formating and the page looks like :

[![](/assets/images/2017-08-09-22_10_18-AngularMyApp-300x189.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-09-22_10_18-AngularMyApp.png)

As we see from the screen above, there is no bootstrap formatting anymore, buttons are missing images (font\_awesome as part of bootstrap also missing).

Now we will add bootstrap-scss and compile our own version of bootstrap css.

First we create new custom variables file and copy  all bootstrap theme variables to it. That way we will be able to customize any bootstrap setting simply by changing our copy of variable.

Create new file "\_custom\_variables.scss" and copy content of the bootstrap \_variables.scss file into it or just copy a file into your scss folder. All files named with underscore are meant to be imported in another scss file by the Sass standard. Files without underscore are compiled to CSS format.

$cp node\_modules/bootstrap-sass/assets/stylesheets/bootstrap/\_variables.scss src/assets/scss/\_custom\_variables.scss

Now we import bootstrap and you variables file into main.scss file:

// Sass compiler need information where icons/fonts are with absolute path definition, tilde character (~) works from any project folder  
$icon-font-path: '~bootstrap-sass/assets/fonts/bootstrap/';

@import "\_custom\_variables.scss";
@import "~bootstrap-sass/assets/stylesheets/\_bootstrap.scss";

If everything works as expected we will get our original bootstrap design back and we can start modifying it, for example we will change primary button color to red. Change variable  $brand-primary in the file \_custom\_variables.scss to red color:

[![](/assets/images/2017-08-09-22_22_29-_custom_variables.scss-—-AngularMyApp-—-Visual-Studio-Code-300x235.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-09-22_22_29-_custom_variables.scss-—-AngularMyApp-—-Visual-Studio-Code.png)

And the result should be visible immediately on the screen :

[![](/assets/images/2017-08-09-22_18_54-AngularMyApp-300x75.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-09-22_18_54-AngularMyApp.png)

The button defined in the currency-list component with the class "btn-primary"  is now red as expected.
