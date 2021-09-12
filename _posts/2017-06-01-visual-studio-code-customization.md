---
layout: post
title: "Visual Studio Code - customization"
date: "2017-06-01"
categories: 
  - "angular"
  - "programming"
  - "typescript"
tags: 
  - "angular"
  - "visual-studio-code"
---

## How to customize your visual studio code

Custom settings are saved in "settings.json" file, you can open it with Ctrol+Shift+P,  search for **Preference: Open user settings .**

### Custom terminal shell - BASH

If you wish to change shell from default windows powershell to cygwin bash terminal for example, use next setting:

"terminal.integrated.shell.windows": "C:\\\\cygwin64\\\\bin\\\\bash.exe"

Of course you will need to set proper folder where your cygwin is installed on your machine.

### Change size of fonts and window appearance

    "editor.fontSize": 14,
    "window.zoomLevel": 2,

###### GIT integration enable/disable

If you do not won't GIT integration (doesn't work good with cygwin git version on windows for example) use :

"git.enabled": false,

But, you will need to type git commands manually if you do that.

# [Angular v4 Typescript Snippets](https://marketplace.visualstudio.com/items?itemName=johnpapa.Angular2)

The Visual Studio Code plugin for code snippets from JohnPapa.

Ctrl+P => ext install Angular2

# [Angular Language Service](https://marketplace.visualstudio.com/items?itemName=Angular.ng-template)

The Visual Studio Code plugin for Html editor intellisense from Angular team.

Ctrl+P => ext install ng-template

#  [GIT History](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)

Plugin for GIT file compare and history/log checking.

Install :

Ctrl+P => ext install githistory

Use:

Ctrl+Shift+P => search for "Git History"
