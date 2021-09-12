---
layout: post
title: "Bash commands"
date: "2016-12-10"
categories: 
  - "programming"
---

# 1\. Some handy bash commands

#### Diff

Compare two files and get list of differences.

$ diff file1 file2

#### Link

Create softlink 
ln -s {/path/to/file-name} {link-name}

Delete softlink
rm {link-name}

#### Support colors in cygwin bash terminal

Open  file ".bashrc" in your home folder and uncomment commands aliases with some additional parameters as "--color":

alias grep='grep --color'                     # show differences in colour
alias egrep='egrep --color=auto'              # show differences in colour
alias fgrep='fgrep --color=auto'              # show differences in colour
#
# Some shortcuts for different directory listings
alias ls='ls -hF --color=tty'                 # classify files in colour
alias dir='ls --color=auto --format=vertical'
alias vdir='ls --color=auto --format=long'
alias ll='ls -l'                              # long list
alias la='ls -A'                              # all but . and ..
alias l='ls -CF'                              #

#### ssh

You can ssh into machine with command:

ssh -l username -p port hostname
ssh username@hostname -p port

ssh ibus@localhost -p 3022

 

### 2\. Add bash.exe as terminal in Visual Studio Code

Open user settings profile and add command:

"terminal.integrated.shell.windows": "C:\\\\cygwin64\\\\bin\\\\bash.exe"

The terminal is opened inside IDE as "Terminal window" with current project folder.

[![](/assets/images/2017-06-15-23_39_57-settings.json-—-myapp01-—-Visual-Studio-Code-300x109.png)](http://bisaga.com/blog/wp-content/uploads/2016/12/2017-06-15-23_39_57-settings.json-—-myapp01-—-Visual-Studio-Code.png)

 

### 3\. Add bash.exe as terminal in IntelliJ IDEA IDE

Open "File/Settings/Terminal" and enter shell path in application settings section :

[![](/assets/images/2017-06-15-23_42_41-Settings-300x157.png)](http://bisaga.com/blog/wp-content/uploads/2016/12/2017-06-15-23_42_41-Settings.png)

When you open terminal you get bash shell inside IDE :

[![](/assets/images/2017-06-15-23_43_53-bsgtime-C__Bisaga_Workspaces_bisaga_time_server_bisaga_time-time.server--300x90.png)](http://bisaga.com/blog/wp-content/uploads/2016/12/2017-06-15-23_43_53-bsgtime-C__Bisaga_Workspaces_bisaga_time_server_bisaga_time-time.server-.png)
