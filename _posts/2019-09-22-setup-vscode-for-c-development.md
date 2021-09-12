---
layout: post
title: "Setup VSCode for C# development"
date: "2019-09-22"
categories: 
  - "blazor"
  - "c"
  - "installation"
  - "programming"
---

**Ubuntu 18.04 , 19.04**

Dotnet Sdk 3.0 release installation

[Install it directly](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/sdk-current) from the software repository.

Mono

Remove all versions of **mono** completely from the system, vscode & omnisharp comes with the mono included, the incorrect version on the system will interfere with the embedded runtime and will not work correctly.

 igorb@desktop:~$ **mono --version**
 Command 'mono' not found, but can be installed with:
 sudo apt install mono-runtime 

VSCode installation

Download [VSCode](https://code.visualstudio.com/download) and install it.

Open downloaded deb file with "Open with the software install" and install it.

**Hide some folders from file explorer**

As explained [here](https://stackoverflow.com/questions/30140112/how-do-i-hide-certain-files-from-the-sidebar-in-visual-studio-code) just set files to exclude.

Search for "**files:exclude**" and add **bin** and **obj** folder to the list of excluded entities.

\*\*/bin
\*\*/obj

Add to favorites on Ubuntu

This is maybe some odd advice but, when you add Code to favorite applications (to the launcher) you do that **from the Show Applications** menu!

If you start code from the terminal and mark the newly showed icon as "Add to favorites", the Code somehow doesn't compile and debug correctly. There are some strange errors after successfully built the solution...

C# project build errors

In case of errors on build, make sure you have only one dotnet sdk installed on your system (ubuntu).

igorb@desktop:~$ **dotnet --list-sdks**
3.0.100 \[/usr/share/dotnet/sdk\]

Omnisharp project build errors

In case of any errors on the project load (OmniSharp log in the output terminal), like :

OmniSharp.MSBuild.ProjectManager        Failed to load project file '/mnt/development/GitHub/Bisaga/About.Application/About.Application.csproj'.

Add build path to the **/etc/profile** file, do not forget to re-login:

export MSBuildSDKsPath="/usr/share/dotnet/sdk/$(dotnet --version)/Sdks"

Change number of notifying instances

echo fs.inotify.max\_user\_instances=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
