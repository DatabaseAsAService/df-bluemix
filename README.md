## DreamFactory 2.0 on Bluemix

[![License](https://poser.pugx.org/dreamfactory/dreamfactory/license.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/dreamfactorysoftware/dreamfactory?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## Overview

DreamFactory(™) is an open source REST API backend for mobile, web, and IoT applications. 
It is built on top of the Laravel framework, and as such retains the requirements of the [Laravel v5.1 framework]

* Get powerful, reusable, documented APIs for SQL, NoSQL, files, email, push notifications and more. In seconds.
* Use server-side scripts to easily customize API behavior at any endpoint, for both API requests and API responses.
* Secure every API endpoint with user management, SSO authentication, role-based access control, OAuth and Active Directory integration.

This repository contains all the files and instructions need to get DreamFactory(™) running on IBM's Bluemix platform.

Learn more at our [website](https://www.dreamfactory.com).

## Documentation

Documentation for the platform can be found on the [DreamFactory wiki](http://wiki.dreamfactory.com).

## Required software and extensions

Check our [wiki installation page](http://wiki.dreamfactory.com/DreamFactory/Installation) for the minimum 
software and extensions required for your system to successfully install and run DreamFactory 2.0.  The Bluemix configuration 
provided as part of the repository will install the mimimum software and extenstions required to run DreamFactory 2.0 on Bluemix.

## Supported Databases

DreamFactory 2.0 on Bluemix currently supports the following databases:

* MySQL and other MySQL compatible database/database services such as ClearDB
* MongoDB
* Postgres

> _Note: Only MySQL is supported for the system database on Bluemix._

## Quick Setup

These instructions allow you to quickly install DreamFactory 2.0 on your system and try it out. 
The commands shown here are primarily for a Linux based OS, but this should also work on Windows and MacOS with all the required software and extensions installed.

> _Note: This quick setup instruction assumes that you are familiar with the Bluemix service, git and the basics of how to use the Cloud Foundry Command Line Interface._

* Install the Cloud Foundry Command Line Interface (https://github.com/cloudfoundry/cli/releases).

> _Windows Users: The Cloud Foundry command line interface is not supported by Cygwin. Use the Cloud Foundry command line interface in a command line window other than the Cygwin command line window._

* Clone this repository to a directory on your system.

> git clone https://github.com/dreamfactorysoftware/df-bluemix.git ~/df2-bluemix

* Change to that directory.

> cd ~/df2-bluemix

* Clone the DreamFactory 2.0 repository into a directory named htdocs

> git clone https://github.com/dreamfactorysoftware/dreamfactory.gif htdocs

* Connect to Bluemix

> cf api https://api.ng.bluemix.net
>
> See (https://www.ng.bluemix.net/docs/starters/install_cli.html) for further discussion on using Cloud Foundry CLI to deploy
> an application to Bluemix

* Login to Bluemix

> cf login -u user@example.com -o YourOrg -s name_of_space

* Create a User Provided Service to connect to a MySQL server/service.

> cf cups appname-db -p "hostname, port, name, username, password"
>
> You will be prompted to enter the hostname, port, name of the database, username and password by the _cf cups_ command  The appname
> is the subdomain, _i.e., appname.mybluemix.net_
>
> Note: During testing, it was determined that the free ClearDB service available from the Bluemix catalog does not allow sufficient
> concurrent connections to properly operate DreamFactory 2.0.  It is recommended that you use an external provider such as ClearDB or
> Amazon RDS directly or run your own MySQL server.  DreamFactory 2.0 on Bluemix has not been tested using the experimental Bluemix
> MySQL service at this time.

* Edit manifest.yml and replace all instances of ##appname## with your application name and save the file

> _Example manifest.yml after editing_

```
---
applications:
- name: df2
  memory: 1024M
  buildpack: https://github.com/cloudfoundry/php-buildpack
  env:
    CF_STAGING_TIMEOUT: 15
    CF_STARTUP_TIMEOUT: 15
    BP_DEBUG: "True"
  services:
   - df2-db
   ```
   
* Deploy DreamFactory 2.0 to Bluemix

> cf push appname
>
> If DreamFactory was sucessfully deployed to Bluemix, the last 2 lines of the output from the _cf push_ command should look something
> like this:
>

```
      state     since                    cpu    memory         disk         details
 #0   running   2016-01-22 02:08:45 PM   0.0%   150.5M of 1G   438M of 1G
```

> Note: If you use a MySQL service from the Bluemix catalog, you will need to determine the service key that is used in the JSON
> array in the VCAP_SERVICES environment variable.  Unfortanetly, you typically can not determine this until _after_ the application
> is running on Bluemix.  Once you have this, you will need to update the __BM_DB_SERVICE_KEY__ entry in the .env file.  The default
> setting, _user-provided_ is for any service created using _cf cups_.  The free ClearDB service uses _cleardb_.
