---
title: "PowerShell startup script"
date: "2017-06-13"
categories: 
  - "programming"
---

# PowerShell on windows 10

To allow script files execution on your machine, you need to [change security policy](https://technet.microsoft.com/en-us/library/ee176961.aspx) and allow that.  To execute command and change policy you need to run powershell console **as administrator**.

Set-ExecutionPolicy RemoteSigned

## How to create startup script

Create **profile.ps1** file and put it into one of default folders (C:\\Windows\\System32\\WindowsPowerShell\\v1.0).

### Beep on backspace

To disable annoying beep when press on backspace if there is no character left, add next command to the startup script:

Set-PSReadlineOption -BellStyle None
