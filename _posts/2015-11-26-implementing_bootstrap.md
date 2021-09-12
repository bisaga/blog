---
layout: post
title: "Worklog Part 1: Building web application with Bootstrap"
date: "2015-11-26"
categories: 
  - "php"
  - "programming"
tags: 
  - "bootstrap"
  - "bower"
  - "php"
  - "silex"
  - "worklog"
---

# Integrating Start Bootstrap template
{% comment %}

We will implement bootstrap based web application theme in Silex application. The best way to start out is with already [available theme](http://startbootstrap.com/template-overviews/sb-admin-2/).

To install it into our application just execute bower install command in application web folder:

H:\\Ampps\\www\\worklog\\web>bower install startbootstrap-sb-admin-2

After installation of all required components (including jquery, bootstrap  and a few others) we will need some help how to use them. You can [download sample implementation](https://github.com/IronSummitMedia/startbootstrap-sb-admin-2/archive/v1.0.7.zip) and start learn directly from included sample application.

After unzipping sample into local www folder we can navigate to it and start inspecting how it is constructed.

[![2015-11-26 19_08_40-SB Admin 2 - Bootstrap Admin Theme](assets/images/2015-11-26-19_08_40-SB-Admin-2-Bootstrap-Admin-Theme-300x222.png)](http://ironsummitmedia.github.io/startbootstrap-sb-admin-2/pages/index.html)

This beautiful sample contain almost everything what is needed to build very powerful web application !

Next step is converting part of this sample code into our application.

## Integrating template in [silex](http://silex.sensiolabs.org/) application

We need to analyze index.html file from the sample and transfer definitions into our **base.html.twig** file.  As you can see in the content of twig file, we only copy "link" and "script" nodes to our file and adjust locations of used resources.

After you have everything in place the response after loading index.php should be something like this (Firefox/Developer tools/Network screen):

[![2015-11-26 19_46_09-Network - http___localhost_worklog_web_index.php](assets/images/2015-11-26-19_46_09-Network-http___localhost_worklog_web_index.php_-300x164.png)](http://bisaga.com/blog/wp-content/uploads/2015/11/2015-11-26-19_46_09-Network-http___localhost_worklog_web_index.php_.png)

All resources should be loaded successfully .

The content of twig base file:

{# Base page template #}
<html>
<head>
    {% block head %}
        <!-- Standard HTML head -->
        <meta charset="UtF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>{% block title %}{% endblock %} - Bisaga Worklog</title>
        {% block stylesheets %}{% endblock %} 
        
        <!-- FAVICON -->
        <link rel="icon" type="image/x-icon" href="{{app.request.basepath}}/favicon.ico" />
        
        <!-- Bootstrap Core CSS -->
        <link rel="stylesheet" href="{{app.request.basepath}}/bower\_components/bootstrap/dist/css/bootstrap.min.css" />

        <!-- MetisMenu CSS -->
        <link href="{{app.request.basepath}}/bower\_components/metisMenu/dist/metisMenu.min.css" rel="stylesheet">

        <!-- Timeline CSS -->
        <link href="{{app.request.basepath}}/bower\_components/startbootstrap-sb-admin-2/dist/css/timeline.css" rel="stylesheet">

        <!-- SB-Admin CSS -->
        <link href="{{app.request.basepath}}/bower\_components/startbootstrap-sb-admin-2/dist/css/sb-admin-2.css" rel="stylesheet">

        <!-- Morris Charts CSS -->
        <link href="{{app.request.basepath}}/bower\_components/morrisjs/morris.css" rel="stylesheet">

        <!-- Custom Fonts -->
        <link href="{{app.request.basepath}}/bower\_components/font-awesome/css/font-awesome.min.css" rel="stylesheet" type="text/css">

        <!-- Worklog custom CSS -->
        <link rel="stylesheet" href="{{app.request.basepath}}/css/worklog.css" />
        
        <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
        <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
        <!--\[if lt IE 9\]>
          <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
          <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
        <!\[endif\]-->          
    {% endblock %}    
</head>
<body>
    <div id="content">{% block content %}{% endblock %}</div>
    <div id="footer">
        {% block footer %}
            &copy; Copyright 2015 by <a href="http://bisaga.com/">Bisaga</a>.
        {% endblock %}
    </div>
    {% block javascripts %}{% endblock %}
    <!-- jQuery -->
    <script src="{{app.request.basepath}}/bower\_components/jquery/dist/jquery.min.js"></script>
    
    <!-- Bootstrap Core JavaScript -->
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="{{app.request.basepath}}/bower\_components/bootstrap/dist/js/bootstrap.min.js"></script>    

    <!-- Metis Menu Plugin JavaScript -->
    <script src="{{app.request.basepath}}/bower\_components/metisMenu/dist/metisMenu.min.js"></script>

    <!-- Morris Charts JavaScript -->
    <script src="{{app.request.basepath}}/bower\_components/raphael/raphael-min.js"></script>
    <script src="{{app.request.basepath}}/bower\_components/morrisjs/morris.min.js"></script>

    <!-- Custom Theme JavaScript -->
    <script src="{{app.request.basepath}}/bower\_components/startbootstrap-sb-admin-2/dist/js/sb-admin-2.js"></script>
    
</body>
</html>

Worklog application folder now grow little larger because of new front-end components in bower\_components folder.

[![2015-11-26 20_03_12-Programmer's Notepad - [index.html]](assets/images/2015-11-26-20_03_12-Programmers-Notepad-index.html-109x300.png)](http://bisaga.com/blog/wp-content/uploads/2015/11/2015-11-26-20_03_12-Programmers-Notepad-index.html.png)

At this point our web application is almost empty, but we have all  necessary components ready to use.

This example application is available on the [Github](https://github.com/bisaga/Worklog).
{% endcomment %}
