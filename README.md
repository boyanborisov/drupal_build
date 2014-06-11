## Introduction

Drupal build is a phing project which aims to improve performance of the drupal developer with automation of some routine tasks like create a project, update a project, upgrade drupal core.
Also it creates a general workflow which aims to facilitate the Continuous integration (CI) workflows.

Drupal build expects the drupal project to use Git as version control system and to have the following structure.

- Drupal core is placed in the root of the repo
- contrib modules are placed in sites/all/modules/contrib
- custom modules are placed in sites/all/modules/custom
- features modules are placed in sites/all/modules/features
- custom profile which must have all the required module listed as dependencies in the profile info file. Note that you should add here only production modules. Avoid module like devel and etc. There is an another way to install dev modules
- the latest versions of features and devel modules should be in the codebase

## Prerequirments

- Install pear
http://pear.php.net/manual/en/installation.getting.php

- Install drush - https://github.com/drush-ops/drush - drush 6.x it work with php 5.3.3+.

```
$ pear channel-discover pear.drush.org
$ pear install drush/drush-6.0.0
```
- Install VersionControl_Git pear package

```
$ pear install VersionControl_Git-0.4.4
```

- Install phing - http://www.phing.info/

```
$ pear channel-discover pear.phing.info
$ pear install phing/phing
```

- Install  PHP CodeSniffer

```
pear install PHP_CodeSniffer
```

- install git >= 1.8


## How To Start A New Project 

In order to start a new project which used Drupal build tool folow the steps:

- create a new empty git repository
- create an empty directory and clone the Drupal build tool in it

```
$ mkdir new_project
$ cd new_project
$ git clone git@github.com:boyanborisov/drupal_build.git .
$ git checkout -b new_project
$ git push origin new_project
```

- copy build.default.properties into build.properties in order to overwrite the default properties.  The most important settings that should be overwritten so far are:

```
drupal.db.url = mysql://dbuser:dbpass@dbhost/dbname
drupal.repo_url = # path to the git repo git@github.com:boyanborisov/sandbox.git
```

```
$ cp  build.default.properties build.properties
```

- Then run 

```
$  phing project-create
```

This command will download latest stable version of drupal and will place it in a folder called www in the current directory.
The phing target will download also the latest versions of devel and features modules and will place them in sites/all/modules/contrib folder. Also a new custom profile will be created based on the Standard drupal profile. Finally the whole www folder will be pushed in a dev branch in the new project git repository

- Now we have the codebase but the site is still not installed. Run following command in order to install the site:

```
$ phing site-install
```

The site will be installed with super user admin and password 1234. Note that you should point the root of the apache virtual host to point to www folder of the project

## How To Setup An Existing Project

In order to setup an existing project which used Drupal build tool folow the steps:

- create an empty directory and clone the Drupal build tool in it

```
$ mkdir new_project
$ cd new_project
$ git clone git@github.com:boyanborisov/drupal_build.git .
$ git checkout new_project
```

- copy build.default.properties into build.properties in order to overwrite the default properties.  The most important settings that should be overwritten so far are:

```
drupal.db.url = mysql://dbuser:dbpass@dbhost/dbname
drupal.repo_url = # path to the git repo git@github.com:boyanborisov/sandbox.git
```

```
$ cp  build.default.properties build.properties
```

- Then run 

```
$  phing site-setup
```

This phing target will clone the new_project codebase in the www folder and will install the site with super user admin and password 1234. Note that you should point the root of the apache virtual host to point to www folder of the project

## How To Update An Installed Site 

Update of the already installed site is really easy. It's a just calling of one command which takes care for everything.

```
phing site-update
```

This phing target will do the following:

- take care to update the codebase in www folder
- it will update drupal db if needed
- it will enable all new modules described in the custom profile info file
- it will revert all the features except these list it in drupal.feature_exclude

## How To Migrate My Site To Drupal Build 

- be sure that you meet all the Prerequirments listed [here](home)
- be sure that your project follows the required project structure
  * Drupal core is placed in the root of the repo
  * contrib modules are placed in sites/all/modules/contrib
  * custom modules are placed in sites/all/modules/custom
  * features modules are placed in sites/all/modules/features
  * custom profile which must have all the required module listed as dependencies in the profile info file. Note that you should add here only production modules. Avoid module like devel and etc. There is an another way to install dev modules
  * the latest versions of features and devel modules should be in the codebase
- Create a new project branch in the drupal_build repository

```
$ mkdir new_project
$ cd new_project
$ git clone git@github.com:boyanborisov/drupal_build.git .
$ git checkout -b new_project
$ git push origin new_project
```

- That's it! Now you could continue from [How To Setup An Existing Project ](how-to-setup-an-existing-project)

## How To Upgrade A Contrib Module 

Upgrade of contrib module could be a really tricky task when you don't know what is the status of the code. Whether the module is "hacked" and so on. There is potential issue to overwrite module's customizations if they are not properly described.
Drupal build tool comes with phing target that will take care for the whole process. Upgrade of a module is just calling of a single target.

```
phing project-upgrade-module
``` 

After execution of the command above you will promoted with "Which module do you want to upgrade [devel] ?". Just write the module that you want to upgrade and continue.

Afterwards you will promoted with the question "Which version do you want to upgarde to(7.x-2.x-dev,7.x-2.0,7.x-1.x-dev,7.x-1.0").
Which is awesome ;) You could select any of the listed versions.

If everything goes fine the target will finish without an error!

But if you have made a hack which is not compatible with the new version of the module you will receive an error. Than you should resolve the conflict manually.

##  How To Upgrade The Drupal Core 

Upgrade of the Drupal core  could be a really tricky task when you don't know what is the status of the code. Whether the code is "hacked" and so on. There is potential issue to overwrite code's customizations if they are not properly described.
Drupal build tool comes with phing target that will take care for the whole process. Upgrade of the core is just calling of a single target.

```
phing project-upgrade-core
``` 
Initially targets clone the drupal and this could take a white ~ 5 mins.


Afterwards you will promoted with the question "Which version do you want to upgarde to(7.x-dev,7.26).
Which is awesome ;) You could select any of the listed versions.

If everything goes fine the target will finish without an error!

But if you have made a hack which is not compatible with the new version of the core you will receive an error. Than you should resolve the conflict manually.
