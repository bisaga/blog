---
layout: post
title: "Angular and Git project setup"
date: "2017-06-26"
categories: 
  - "angular"
  - "programming"
  - "typescript"
tags: 
  - "angular-cli"
  - "git"
  - "github"
  - "visual-studio-code"
---

# Angular-CLI, VSCode and Git/GitHub configuration

I will create Angular 4 client application with project setup needed to seamlessly develop and store source code to remote Github repository.

## Projects folder structure

I will create client side SPA application as client to Java spring boot server application, [created in previous blog article](http://bisaga.com/blog/programming/java-spring-boot-project-setup/).

Both projects ([server](https://github.com/bisaga/SpringBootMyApp) & [client](https://github.com/bisaga/AngularMyApp)) are in separate folders, only at the deployment stage they will be combined under java server application. In the simplest micro-service deployment scenario, both applications are served from the same server instance. Embedded Tomcat server  will serve all files needed for angular client side application and to drive java REST services as application back-end.

myapp
!--server
!  !--SpringBootMyApp
!     
!--client
   !--AngularMyApp

## Generate angular client project

After you have installed [all prerequisites](http://bisaga.com/blog/programming/angular-environment/) for angular you create a new empty application from command line. Command will create application subfolder and all required app files for simple angular app.

/myapp/client/$ ng new AngularMyApp

You can experiment with some command line switches to add additional generator options like generate routes file for example (**\--routing**).

###### Dry-Run

If you do not want to actually execute commands then add **\--dry-run** option ("-d"  for short) and inspect generated set of commands.

$ ng new app\_name --routing 
$ ng new app\_name --dry-run 
$ ng new app\_name -r -d

## Open project in [Visual Studio Code](https://code.visualstudio.com)

You can open project from command line ("code .") or open folder from inside editor.

# [Integrate with Git  source control](http://www.hongkiat.com/blog/version-control-git-vs-code/)

After angular-cli create a project it will immediately initialize GIT folder and commit initial version of an application.

You can check log of git commits right after generating new app :

/AngularMyApp/$ git log

and result will be something like this :

Author: Angular CLI <angular-cli@angular.io>
Date:   Mon Jun 26 22:41:07 2017 +0200

chore: initial commit from @angular/cli

We see initial commit was done by @angular/cli tool.

### [Configure remote](https://help.github.com/articles/set-up-git/) Git repository

If you wish to publish code to the Git-Hub server, you need to [prepare local windows git](https://help.github.com/articles/setting-your-username-in-git/) to be able to push code to remote server without any username/password interaction.

git config --global credential.helper wincred

git config --global user.name "user\_name"

git config --global user.email "email@example.com"

This type of user identification is used with **HTTPS repository address** for git communication.

You need to have github repository created (**empty project**!). When you create new repository, try to not create **readme file**, because you will not be able to push initial code up without "force" option. This option is not possible inside visual studio code. "Force push" is possible with command line or with github desktop client.

###### With remote repository created configure "git remote"  localy with correct "https" repository address :

/AngularMyApp/ $> git remote add AngularMyApp https://github.com/bisaga/AngularMyApp.git

###### Push code to Github

After adding remote repository to local git configuration you select "Push to..." **inside visual studio code** Git extension menu (...), select remote repository and push.

## SSH key passphrase

You need to [setup your ssh-agent](https://help.github.com/articles/working-with-ssh-key-passphrases/) correctly to work with remote git repository without constant interruptions with an question for ssh passphrase.

If you use bash terminal inside vscode and you work on windows, add the ssh-agent to the setup script in your home (~) folder (usually c:\\Users\\your\_name\\.bash\_profile file.

env=~/.ssh/agent.env

agent\_load\_env () { test -f "$env" && . "$env" >| /dev/null ; }

agent\_start () {
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ; }

agent\_load\_env

# agent\_run\_state: 0=agent running w/ key; 1=agent w/o key; 2= agent not running
agent\_run\_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if \[ ! "$SSH\_AUTH\_SOCK" \] || \[ $agent\_run\_state = 2 \]; then
    agent\_start
    ssh-add
elif \[ "$SSH\_AUTH\_SOCK" \] && \[ $agent\_run\_state = 1 \]; then
    ssh-add
fi

unset env

You can check with the command

git fetch

If you get the question then you probably need to create proper ssh-agent setup.

 

 

### Links

Initial code for [AngularMyApp is on this github repostory](https://github.com/bisaga/AngularMyApp).
