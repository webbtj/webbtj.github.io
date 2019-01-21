---
layout:  post
title:   "ProTip: Binary Search in Real Life"
date:    2017-02-10 11:00:00 -0300
preview: "A tool from computer science you can use to speed up certain types of
problem solving."
---
#### What is binary search?
Without getting too technical, binary search is actually pretty simple.
Binary search is taking the collection of things you have to search through and
cutting them in half. If the fist half satisfies your search, cut that in half
and search again, if it doesn't match cut the second half in half and search
again. Keep repeating until you have a group of one item that satisfies your
search.

#### Clarity by example
So, that might not be crystal clear, so let's try a real life example. I'm going
to reference managing a blog with many plugins installed, and one plugin is
causing the site to not function, but we don't know which one.

##### Traditional approach (potentially really slow)
The initial approach might be to disable all of the plugins and start enabling
them one at a time until the issue comes back (or disable them one at a time
until the issue is resolved).

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
