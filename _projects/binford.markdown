---
layout: project
title:  "Binford"
date:   2016-03-27 20:37:08 -0300
categories: automation wordpress wasp
github: https://github.com/webbtj/binford
preview: "Binford automates the process of exposing custom fields created with
Advanced Custom Fields in WordPress to the appropriate php and Smarty tpl
files. Binford works well with Taylor."
---
## Overview

Binford is the "other piece" of [Taylor](/projects/taylor). Binford automates the process of
exposing custom fields created with Advanced Custom Fields to the appropriate php and Smarty tpl
files.

## The Problem

[Taylor](/projecrs/taylor) automated most of the basic stuff already. It would be nice to automate
the process of adding custom fields to page templates and post types, but to stuff them into a json
file, especially when ACF opens up so many options, seems counter intuitive. ACF has already built
a great web ui for managing these fields, so why reinvent the wheel? So the problem remained,
how can I automate this boring, repetitive chore, so that I can spend more time solving new, more
challenging problems?

## The Solution

Of course, this is where Binford comes in. Binford is a pretty simple, single purpose utility.
Once custom fields have been created with Advanced Custom Fields, the user can run Binford which
will automatically scan the WordPress site for ACF fields and modify the appropriate php and Smarty
templates to expose those custom fields as variables. Binford will treat the variables based on their
custom field data types (repeaters are treated as array), post lists are iterated and populated with
necessary properties, etc.

Like [Taylor](/projects/taylor), Binford is open source, but is intended to be distributed as a single
phar archive. Because of this the source of Binford ships with a compiler (really an "archiver")
called Harper (there's a really obscure theme here). The Binford phar archive can exist anywhere
within the WordPress install (root, in a plugin, in a theme, doesn't matter).
