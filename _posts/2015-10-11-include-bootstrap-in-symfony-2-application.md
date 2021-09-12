---
layout: post
title: "Include Bootstrap in Symfony 2 application"
date: "2015-10-11"
categories: 
  - "php"
  - "programming"
tags: 
  - "bootstrap"
  - "bower"
  - "symfony"
---

## [Bootstrap](http://getbootstrap.com/)

{% comment %}

The best tool to design nice, responsive, mobile first web applications is bootstrap framework. It is  also integrated as [form design theme](http://symfony.com/blog/new-in-symfony-2-6-bootstrap-form-theme) in symfony.

Before you can use it in your [symfony](http://symfony.com/) application, you will need to install it.

The easiest way is with a web front-end package manager - [bower](http://symfony.com/doc/current/cookbook/frontend/bower.html). You should install [node](https://nodejs.org/en/) first and then install bower with [npm package manager](https://www.npmjs.com/package/bower).  Then including resources in your twig templates is as easy as use [asset component](http://symfony.com/blog/new-in-symfony-2-7-the-new-asset-component).

Isn't this little to much to start with ?

#### Shortcut

If you don't know what node/npm/bower is or if you don't want to know (for now) :-) , you can just use bootstrap directly from publicly accessible  [CDN links](http://getbootstrap.com/getting-started/#download-cdn). Do not forget to include jquery library first, it is required by bootstrap.

<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">        
        <title>{% block title %}Bisaga application{% endblock %}</title>
        {% block stylesheets %}{% endblock %}
        <link rel="icon" type="image/x-icon" href="{{ asset('favicon.ico') }}" />
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
        <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
        <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
        <!--\[if lt IE 9\]>
          <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
          <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
        <!\[endif\]-->        
    </head>
    <body>
        {% block body %}{% endblock %}
        {% block javascripts %}{% endblock %}
        <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
        <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
        <!-- Include all compiled plugins (below), or include individual files as needed -->
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
    </body>
</html>

Just don't forget, [if you take shortcuts, you get cut short](http://www.brainyquote.com/quotes/quotes/g/garybusey457780.html?src=t_shortcuts).

## Using [bower](http://bower.io/)

Why we needs front-end package management ?  With front-end package manager we simplify **installing and updating** project dependencies for client side libraries. Bower also check inter-dependencies between those libraries and won't install the package incompatible with one that's already installed.

If you use bower for front-end package management  and don't want to use default download directory ("bower\_components"), just create local config file ".bowerrc":

{
  "directory": "libs"
}

Then you can install bootstrap with a command :

H:\\Ampps\\www\\webapp05\\www\\> bower install --save bootstrap

You will get two sub folders, "bootstrap" and "jquery", inside "libs" folder.

### [Asset component](http://symfony.com/blog/new-in-symfony-2-7-the-new-asset-component)

With [asset component](http://symfony.com/blog/new-in-symfony-2-7-the-new-asset-component) we generate URL addresses of web assets such as CSS, stylesheets, graphics and Javascript files. This means that you can change location of assets at one place  in configuration file.

We create new  "assets" section in "framework"  section of config.yml file and add three "packages" inside :

framework:
    assets:
        packages:
            jquery:
                base\_path: /libs/jquery/dist/
            bootstrapjs:
                base\_path: /libs/bootstrap/dist/js/
            bootstrapcss:
                base\_path: /libs/bootstrap/dist/css/

With that in place we can define URL location in our template file with asset function, for example:

 <link rel="stylesheet" href="{{ asset('bootstrap.min.css', 'bootstrapcss') }}">

Second parameter in asset function is package location from config.yml file.

And the final twig file with all required includes for bootstrap is :

<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">        
        <title>{% block title %}Bisaga application{% endblock %}</title>
        {% block stylesheets %}{% endblock %}
        <link rel="icon" type="image/x-icon" href="{{ asset('favicon.ico') }}" />
        <link rel="stylesheet" href="{{ asset('bootstrap.min.css', 'bootstrapcss') }}">
        <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
        <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
        <!--\[if lt IE 9\]>
          <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
          <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
        <!\[endif\]-->        
    </head>
    <body>
        {% block body %}{% endblock %}
        {% block javascripts %}{% endblock %}
        <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
        <script src="{{ asset('jquery.min.js', 'jquery') }}"></script>
        <!-- Include all compiled plugins (below), or include individual files as needed -->
        <script src="{{ asset('bootstrap.min.js', 'bootstrapjs')}}"></script>
    </body>
</html>

{% endcomment %}