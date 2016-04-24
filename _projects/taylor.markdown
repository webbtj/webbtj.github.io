---
layout: project
title:  "Taylor"
date:   2016-04-14 20:37:08 -0300
categories: automation wordpress wasp
github: https://github.com/webbtj/taylor
preview: "Taylor was built to automate all of the boring, repetitive tasks of
the workflow I've developed over the years for custom WordPress development.
Taylor's goal is to allow a basic site to be built by a front-end developer
with little PHP experience and to allow back-end developers to jump right into
more custom and complex functionality."
---
## Overview

Taylor was built to automate all of the boring, repetitive tasks of the workflow I've developed
over the years for custom WordPress development. Typically, my WordPress projects are built on,
what I've been referring to as *WASP* (WordPress, Advanced Custom Fields, Smarty, and Posts-to-Posts).
This always includes several custom post types, various index pages of those post type, as well
as several other page templates. Taylor's goal is to allow a basic site to be built by a front-end
developer with little PHP experience and to allow back-end developers to jump right into more
custom and complex functionality without having to worry about repeating all of the basics.

## The Problem

Setting up a WordPress installation, setting up the database, beginning a new custom theme,
installing most of the same plugins, activating those plugins, activating the theme, bringing your
standard front end libraries into the theme. All of that is very boring, not at all challenging,
and very uninspiring, especially for a senior level developer, yet at the same time is very tedious
and time consuming. Taylor aims to fix that by letting you do all of that mundane work with a single
json file and a single command.

## The Solution

Taylor automates all of these chores and can greatly reduce the setup time of a project. Taylor
is very specific to the workflow I've developed with my team over the years, but it has already
paid dividends in our productions.

The core of Taylor is the taylor.json file, where the user can declare all of their requirements,
including menus, custom post types, post-to-post relationships, static assets, required plugins,
database credentials and well as meta-date like the site name, theme name and path, etc. Taylor
also has built in integration with the [Clone](https://gitlab.com/norex/app_clone) front-end framework by [@joshdrink](https://twitter.com/joshdrink) and [@JBValo](https://twitter.com/JBValo) to allow
a single entry in the json file to install the entire Clone framework within the theme.

Personally, my favourite parts of Taylor would be the automated database setup and the automated
WordPress and plugin installs. By creating one global user with global privileges on my local
database server, I can even automate the process of setting up a database and user for WordPress
to use. And by specifying a WordPress version as well as all of my common plugins those can be
automatically downloaded and installed.

Taylor is open source, but is intended to be distributed as a single phar archive. Because of this
the source of Taylor ships with a compiler (really an "archiver") called Borland (not to be confused
with the Borland C compiler, trust me there's a theme going on here).
