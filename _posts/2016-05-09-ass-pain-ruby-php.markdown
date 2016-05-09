---
layout:  post
title:   "The Ass-Pain That is Ruby + PHP"
date:    2016-05-09 14:18:00 -0300
preview: "A little gotcha for developers working in PHP, specifically using
XAMPP, and doing Ruby development."
---
#### The Ass-Pain 
So this pain in the ass happened to me today.

```
$ brew update
Unknown option: n
Unknown option: 1
Usage: head [-options] <url>...
    -m <method>   use method for the request (default is 'HEAD')
    -f            make request even if head believes method is illegal
    -b <base>     Use the specified URL as base
    -t <timeout>  Set timeout value
    -i <time>     Set the If-Modified-Since header on the request
    -c <conttype> use this content-type for POST, PUT, CHECKIN
    -a            Use text mode for content I/O
    -p <proxyurl> use this as a proxy
    -P            don't load proxy settings from environment
    -H <header>   send this HTTP header (you can specify several)

    -u            Display method and URL before any response
    -U            Display request headers (implies -u)
    -s            Display response status code
    -S            Display response status chain
    -e            Display response headers
    -d            Do not display content
    -o <format>   Process HTML content in various ways

    -v            Show program version
    -h            Print this message

    -x            Extra debugging output
```

#### The Problem
I was trying to use [rvm](https://rvm.io/) to get started on a Rails project.
Several functions, including `brew update` we're throwing the above error.
As it turns out, XAMPP is actually the culprit. Yes, my laziness in Apache/PHP
management came back to bite me in the ass. Why? Because XAMPP has a script
called `HEAD` (named for the HTTP verb) and the path to said script was in my
user's `PATH`.

#### The Solution
I'm not a genius, but I do know how to Google. This answer from StackOverflow
seemed to be just what I needed. The short version is; renaming the `HEAD`
script to `HTTP_HEAD` (in `/Applications/XAMPP/xamppfiles/bin`) worked in terms
of allowing me to progress with rvm. Who knows what'll happen when I switch back
over to PHP development? I guess future TJ can deal with that, I'm a Ruby dev
for the next three weeks.

The long version is read the SO post: [http://stackoverflow.com/questions/21498991/head-usage-unknown-option-1-n-error-possibly-ruby-related](http://stackoverflow.com/questions/21498991/head-usage-unknown-option-1-n-error-possibly-ruby-related)
