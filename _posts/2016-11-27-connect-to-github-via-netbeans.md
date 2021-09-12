---
layout: post
title: "Connect to Github via Netbeans"
date: "2016-11-27"
categories: 
  - "programming"
---

## Create remote git repository

You need to create [new github repository](https://github.com/new) before you can connect local repository to it. Of course you need to have an account on github, but it's not an issue, it's free.

## [![2016-11-27-00_21_32-photos](/assets/images/2016-11-27-00_21_32-Photos-300x125.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_21_32-Photos.png)

On the remote repository find https address of your project and save it for later.

[![2016_11_27_00_00_13_photos](/assets/images/2016_11_27_00_00_13_Photos-300x156.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016_11_27_00_00_13_Photos.png)

## Create local git repository

After creating new project in Netbeans you need to initialize local git repository.

#### Define exceptions - .gitignore file

But before you do that, create ".gitignore" file where you define all exceptions. In the source control we usually save only code files and configurations, but not binary files, IDE specific files etc.  The file must be in the project root folder.

build.xml
/nbproject/
/build/
/web/bower\_components/
/web/node\_modules/

You can always delete exceptions from "gitignore" file and add files to repository later.

#### Initialize

At project node, with the right click, select "**Versioning**" and "**Initialize Git repository**".  Initialization procedure **add** all files in the project, except ignored ones,  to the local repository. Adding a file only mark file for version control. File is not in version control until is committed to.

#### [![git_initialize_netbeans](/assets/images/git_initialize_netbeans-300x86.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/git_initialize_netbeans.png)

#### Commit to local repository

Your files need to be **committed** to local repository. With right click on project node, select "Git" and "Commit".  This will **create local branch in local repository** which will you connect to github.

[![2016-11-26-23_49_55-planitia-no-branch](/assets/images/2016-11-26-23_49_55-Planitia-no-branch-300x279.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-26-23_49_55-Planitia-no-branch.png)In the repository browser (/Team/Repository/Repository Browser) you see current state of local git:

[![2016-11-27-00_11_54-jump-list-for-mozilla-firefox](/assets/images/2016-11-27-00_11_54-Jump-List-for-Mozilla-Firefox-300x145.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_11_54-Jump-List-for-Mozilla-Firefox.png)

## Pull from remote repository

Because you already commit to remote repository, that's because of automatically added **readme.md** and **licence** files, you must pull those files first and **MERGE** them to the local repository.  Then you will be able to push all local differences back to remote repository.

[![2016-11-27-00_27_14-jump-list-for-mozilla-firefox](/assets/images/2016-11-27-00_27_14-Jump-List-for-Mozilla-Firefox-300x201.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_28_05-Pull-from-Remote-Repository.png)Enter project URL (https://....), username and password.

[![2016-11-27-00_28_05-pull-from-remote-repository](/assets/images/2016-11-27-00_28_05-Pull-from-Remote-Repository-300x188.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_28_05-Pull-from-Remote-Repository.png)

Connect branch with local origin branch where files will be synchronized from remote server.

[![2016-11-27-00_28_25-pull-from-remote-repository](/assets/images/2016-11-27-00_28_25-Pull-from-Remote-Repository-300x164.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_28_25-Pull-from-Remote-Repository.png) And finish. You will merge new files (readme.me and licence) from remote to local git.

## Push to remote repository

After you pull new files down or if you didn't have any to pull, you continue with push.

Select right click on project node and execute Git/Remote/Push.

[![2016-11-26-23_56_45-jump-list-for-mozilla-firefox](/assets/images/2016-11-26-23_56_45-Jump-List-for-Mozilla-Firefox-300x197.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-26-23_56_45-Jump-List-for-Mozilla-Firefox.png)Configure  URL of your project (https://....), username and password.

[![2016-11-27-00_04_21-push-to-remote-repository](/assets/images/2016-11-27-00_04_21-Push-to-Remote-Repository-300x200.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_41_58-Push-to-Remote-Repository.png)

Select local branch to push.

[![2016-11-27-00_41_58-push-to-remote-repository](/assets/images/2016-11-27-00_41_58-Push-to-Remote-Repository-300x167.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_41_58-Push-to-Remote-Repository.png)

And select local origin branch where remote repository is replicated. [![2016-11-27-00_42_08-push-to-remote-repository](/assets/images/2016-11-27-00_42_08-Push-to-Remote-Repository-300x172.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-00_42_08-Push-to-Remote-Repository.png)Click Finish and your files will be sent to remote github repository.

## Verify remote git repository

If everything went right, you will see all your project files and folders on the github and two new files inside your local project.

[![2016-11-27-01_11_41-bisaga_planitia_-this-is-another-test-project-to-see-how-new-stuff-working-toget](/assets/images/2016-11-27-01_11_41-bisaga_Planitia_-This-is-another-test-project-to-see-how-new-stuff-working-toget-300x163.png)](http://bisaga.com/blog/wp-content/uploads/2016/11/2016-11-27-01_11_41-bisaga_Planitia_-This-is-another-test-project-to-see-how-new-stuff-working-toget.png)That's it ! You are connected to new github repository.
