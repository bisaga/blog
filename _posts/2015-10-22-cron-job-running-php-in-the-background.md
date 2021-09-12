---
layout: post
title: "Cron job - running PHP in the background"
date: "2015-10-22"
categories: 
  - "php"
  - "programming"
tags: 
  - "database"
  - "godaddy"
  - "mysql"
  - "php"
---

# Background jobs

If you wish to write responsive web applications, you will need to  push some operations in the background. That way you can just register request for some long running task and immediately return to the client.

If you search the web, there are many ways how to achieve this, but not so many implementation are ready to do it in constraint environment of simple web hosting.

My web page is hosted by [GoDaddy](https://www.godaddy.com/?plid=GoDaddy) with so called "Linux hosting with cPanel". I have [PHP](https://secure.php.net/) and [MySql](https://www.mysql.com/), but not much beside this. GoDaddy luckily allows [cron jobs](https://www.godaddy.com/help/create-cron-jobs-3548).  We simply register some command as "cron job" to run unattended at specified frequency.

For proof of concept I will need simple php program and run it as cron job.  At each cron job iteration we will insert one record into database table. We just want to proof that php program can run in the background as  cron job.

Create some database and add "tasklog" table.

create table tasklog (
    id int not null auto\_increment,
    created datetime,
    primary key (id)
)

Our simple PHP program is :

<?php
try {
    $host = "localhost";
    $dbname = "your\_database";
    $user = "your\_db\_user";
    $pass = "your\_password";

    # MySQL with PDO\_MYSQL
    $db = new PDO("mysql:host=$host;dbname=$dbname", $user, $pass);
    $db->setAttribute( PDO::ATTR\_ERRMODE, PDO::ERRMODE\_EXCEPTION );

    $stmt = $db->prepare("INSERT INTO tasklog(\`created\`) VALUES(NOW())");
    $stmt->execute();
  
    $db = null;
}
catch(PDOException $e) {
    echo $e->getMessage();
}

To test it, just put the "taskrun.php" in your "public\_html" folder and navigate to it. If something will go wrong in the program, the settings for exceptions are set to report it to the client. Please test the program until everything not running smoothly.

### Register [cron job](https://www.godaddy.com/help/create-cron-jobs-3548)

You can put program file to any folder. If folder is not under "public\_html" folder, it will be inaccessible from the public web and that way will be much more secure. We create new "jobs" folder under our root home folder and move "taskrun.php" program there.

In the "cPanel" locate "Advanced" section and select "Cron Jobs":

[![2015-10-22 22_42_41-cPanel - Main](images/2015-10-22-22_42_41-cPanel-Main-300x80.png)](http://bisaga.com/blog/wp-content/uploads/2015/10/2015-10-22-22_42_41-cPanel-Main.png)Create new job with a one minute frequency as:

/usr/local/bin/php "$HOME/jobs/taskrun.php" > /dev/null 2>&1

To prevent email to be sent for each iteration, we put redirection into the command ( **> /dev/null 2>&1** ).

[![2015-10-22 22_45_26-cPanel - Cron Jobs](images/2015-10-22-22_45_26-cPanel-Cron-Jobs-300x41.png)](http://bisaga.com/blog/wp-content/uploads/2015/10/2015-10-22-22_45_26-cPanel-Cron-Jobs.png)Wait a minute and check if there are some records in the "tasklog" table. You will see something like this:

[![2015-10-23 00_10_00-n1plcpnl0026.prod.ams1.secureserver.net _ localhost _ bisagasamples _ tasklog _](images/2015-10-23-00_10_00-n1plcpnl0026.prod_.ams1_.secureserver.net-_-localhost-_-bisagasamples-_-tasklog-_--300x279.png)](http://bisaga.com/blog/wp-content/uploads/2015/10/2015-10-23-00_10_00-n1plcpnl0026.prod_.ams1_.secureserver.net-_-localhost-_-bisagasamples-_-tasklog-_-.png)

Success !

Of course, this is only proof of concept for running something unattended in the background. But I think there are already some open source job-task runner out there.
