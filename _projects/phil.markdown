---
layout: project
title:  "Phil"
date:   2016-04-15 20:37:08 -0300
categories: automation apache php
github: https://github.com/webbtj/phil
preview: "Phil automates a couple of mundane tasks that PHP and other Apache
based web-devs find themselves doing on every new project: setting up a new
domain in their /etc/hosts and and adding a new a new entry in their httpd.conf."
---
## Overview

Phil was built to automate a couple of mundane tasks that PHP and other Apache based web-devs
find themselves doing on every new project: setting up a new domain in their `/etc/hosts` and
and adding a new a new entry in their `httpd.conf` (or `vhosts` file).

## The Problem

Every time a web-dev using Apache is starting a new project they need to `sudo` edit their
`/etc/hosts` file, pick a local domain, point it to 127.0.0.1, (sometimes `sudo`) edit their
`httpd.conf` or other file containing their vhosts directives, add that same domain, point it
to a specific directory, create that directory, and restart Apache. All that is outside of
actually setting up the project. Wouldn't it be easier to just run one command that modified
the necessary files, created the necessary directory, and restarted Apache for you? That's
the whole purpose of Phil.

## The Solution

Since Phil is targetted at Apache users, why not write it in something their likely already
using? This is why Phil is written in pure PHP. Phil was put together in a day, and the finishing
touches were the one line install. To keep Phil light and not dependant on the use of specific
package managers such as composer, Phil can be installed with a single PHP command executed in the
terminal.

```
php -r "eval(file_get_contents('http://goo.gl/8bWMsW'));"
```

Of course `eval`ing the contents of a remote file (especially one behind a short link provider)
is terrifying. This was obviously built for convenience, and I only except people I already have
a relationship with to trust this command. But for the curious, if you dig into the short link
you'll see it redirects to a pulic gist which simply grabs a copy of the install script, stores
it in the `/tmp` directory and runs it. Alternatively one can simply download the install script
and run it manually, or for an even more manual process a user can pull the project manually
and set up their own `bash_profile` alias.

Once Phil is installed the user is prompted to initialize Phil which will create the necessary
local files. From there the user can get and set Phil configurations such as the port Apache is
running on, the directory where their web projects are stored, and the paths to their `/etc/hosts`
and `httpd.conf` files. Once Phil is completely configured setting up a new site is as simple as
`phil [domain] [folder]` where "domain" is the local domain you're setting up, and "folder" is the
path it will be served out of. If the directory does not yet exist, it will be created.
