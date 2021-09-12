---
layout: post
title: "Google Drive sync with Grive2 on Ubuntu"
date: "2019-09-23"
categories: 
  - "installation"
  - "programming"
tags: 
  - "google-drive"
  - "grive"
  - "ubuntu"
---

Install Grive2

Detailed instructions [here](http://bookofzeus.com/articles/linux/install-grive2-ubuntu/).

sudo add-apt-repository ppa:nilarimogard/webupd8
sudo apt-get update 
sudo apt-get install grive

The first run to establish a connection and authenticate

mkdir GoogleDrive
cd GoogleDrive

grive -a 

**Grive -a** will give you url to enable access to google drive, returned private key must be copied back to the terminal.

To sync manually you execute "grive" command in the GoogleDrive folder:

 **grive**

Enable Grive2 [auto sync](https://www.linuxuprising.com/2018/08/cli-google-drive-client-grive2-how-to.html) - **better use without it**

By default, this sync process take too much processor strength and make too much network traffic **almost all the time or every 3 seconds**, even when there are no changes on the google drive and the timer is set to 15 minutes.

Maybe default configuration just isn't configured optimally or something. Take this into consideration before continue.

GoogleDrive in the systemctl commands is folder in your $HOME.

systemctl --user enable grive-timer@$(systemd-escape GoogleDrive).timer 
 systemctl --user start grive-timer@$(systemd-escape GoogleDrive).timer 
 systemctl --user enable grive-changes@$(systemd-escape GoogleDrive).service 
 systemctl --user start grive-changes@$(systemd-escape GoogleDrive).service

Systemctl commands results:

Created symlink /home/igorb/.config/systemd/user/timers.target.wants/grive-timer@GoogleDrive.timer → /usr/lib/systemd/user/grive-timer@.timer

Change timer to only fire once in 15 minutes:

**First stop the timer & service:**

systemctl --user stop grive-timer@$(systemd-escape GoogleDrive).timer

systemctl --user stop grive-changes@$(systemd-escape GoogleDrive).service

**Edit the timer file:**

sudo gedit ~/.config/systemd/user/timers.target.wants/grive-timer@GoogleDrive.timer 

**Change the content of the timer setup:**

\[Unit\]
 Description=Google drive sync (fixed intervals)
 \[Timer\]
 OnCalendar=\*:0/15
 OnBootSec=3min
 OnUnitActiveSec=15min
 Unit=grive-timer@%i.service
 \[Install\]
 WantedBy=timers.target

Run the command to reload timers and then start the timer and service again.

systemctl --user daemon-reload

Set permission for GoogleDrive files

Ifyou have google drive folder on second drive, not exactly on "home" folder, then you need to change permissions for folders and files :

sudo chmod -R a+rwx ./Storage
