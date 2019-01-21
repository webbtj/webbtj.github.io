---
layout: project
title:  "mysql-db-clone"
date:   2016-03-02 20:37:08 -0300
categories: nodejs mysql automation
github: https://github.com/webbtj/mysql-db-clone
preview: "mysql-db-clone is a small, single purpose NodeJS tool that copies a
MySQL database from one server to another."
---
## Overview

mysql-db-clone is a small, single purpose NodeJS tool that copies a MySQL
database from one server to another. It was born out of the need to give team
members with little or no database experience, or experience with tools like
`vim` or `sed` and regular expressions a quick an easy way to migrate a database
from one server to another (usually from a staging or production server to a
remote development environment).

## The Solution

mysql-db-clone has a small config json file to input the credentials for the two
databases in question. Once they are in, so long as they are valid, connecting
and copying is as simple as running the node script. There is also the ability
to find an replace a word or phrase. This is specifically useful when the
database is for a website and it stores, for example, the domain the site is
running on.
