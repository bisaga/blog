---
layout: post
title: "Symfony installer, creating new project"
date: "2015-09-04"
categories: 
  - "php"
  - "programming"
tags: 
  - "netbeans"
  - "php"
  - "symfony"
---

#### Symfony installer

[Symfony](https://symfony.com/) is one of the best php frameworks for web development. To start working with symfony we first need to download [symfony installer:](http://symfony.com/doc/current/book/installation.html)

c:\\> php -r "readfile('http://symfony.com/installer');" > symfony

After downloading symfony file, you can copy it to your new projects folder.

Then you use it with php interpreter as a command:

C:\\> php symfony

If you wish to simplify usage of symfony command,  just create dos batch command file (symfony.bat) with next content:

@echo off
call php C:\\cmd\\symfony %\*

Save both files, downloaded symfony file and batch file to a system wide available folder. If you haven't it yet, create one folder (like C:\\cmd) and add it to [system environment PATH](http://www.computerhope.com/issues/ch000549.htm).  New command "symfony" will then be available anywhere on the system. To check it, open DOS command window and type symfony command:

H:\\TEMP> symfony

You should see something like this:

[![2015-09-04 17_44_31-Command Prompt](images/2015-09-04-17_44_31-Command-Prompt-300x284.png)](http://bisaga.com/blog/wp-content/uploads/2015/09/2015-09-04-17_44_31-Command-Prompt.png)

Symfony **installer** installed !

#### Create first project

To create new symfony project, open DOS command line in projects parent folder and enter command :

C:\\ampps\\Ampps\\www\\>symfony new webapp03

As I described in [this blog article](http://bisaga.com/blog/programming/php-development-environment-on-windows/), my development environment consist of locally installed apache web server. This means I don't need to start web server every time I want to serve application manually, but AMPPS application must be started of course.

To open newly created application, enter URL of the project into the browser:

http://localhost/webapp03/web/

You will get application default startup page like:

[![2015-09-06 10_00_35-Welcome! - Opera](images/2015-09-06-10_00_35-Welcome-Opera-300x262.png)](http://bisaga.com/blog/wp-content/uploads/2015/09/2015-09-06-10_00_35-Welcome-Opera.png)

#### Create project in Netbeans with existing source

If you now want to work in [Netbeans](https://netbeans.org/features/php/), just open new project with existing source:

[![2015-09-07 20_47_23-New Project](images/2015-09-07-20_47_23-New-Project-300x182.png)](http://bisaga.com/blog/wp-content/uploads/2015/09/2015-09-07-20_47_23-New-Project.png)

 

Select folder where generated project reside and you are good to go. You can create extra meta data folder for netbeans specific project files. This way you will not to pollute  web folder itself.

[![2015-09-07 20_48_29-New PHP Project with Existing Sources](images/2015-09-07-20_48_29-New-PHP-Project-with-Existing-Sources-300x182.png)](http://bisaga.com/blog/wp-content/uploads/2015/09/2015-09-07-20_48_29-New-PHP-Project-with-Existing-Sources.png)On the last step you should define startup file , this could be **web/app.php** or **web/app\_dev.php,** with some additional development informations from symfony framework in the bottom of the web page.

[![2015-09-07 20_49_26-New PHP Project with Existing Sources](images/2015-09-07-20_49_26-New-PHP-Project-with-Existing-Sources-300x176.png)](http://bisaga.com/blog/wp-content/uploads/2015/09/2015-09-07-20_49_26-New-PHP-Project-with-Existing-Sources.png)
