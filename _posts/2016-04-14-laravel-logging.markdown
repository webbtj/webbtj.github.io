---
layout:  post
title:   "Custom Laravel Logging"
date:    2016-04-14 20:37:08 -0300
preview: "A brief tutorial on building a custom user action logger for Laravel
with Monolog. I'll cover logging different actions to different files and
enabling and disabling action logging at the action and action group levels."
---
#### The Intro

Recently, a client (a very technical client) asked me if we could add "user usage tracking" to their Laravel application. By "user usage tracking" they were really looking for access logs, logging very specific information; when did a user fail to log in, fail their two-factor authentication, edit specific content, etc. Of course there are plenty of logging tools out there, but we wanted complete flexibility and wanted to add some pretty neat features. After a bit of research, [Monolog](https://github.com/Seldaek/monolog) was the clear winner.

This is a somewhat technical article, I'm assuming you have experience with Laravel and Composer.

#### The Customization

Aside from logging these actions, we wanted to add a great deal of control to what was being logged and where. Different "types" of actions may need to be logged to different files, and we may decide later to disable certain actions from being logged.

Since we're using [Laravel](https://laravel.com/) as a framework (in this case 5.1, but this should hold true for 5.2 as well), we have [PHP dotenv](https://github.com/vlucas/phpdotenv) available out of the box, which means we can create environment variables to tell our logger to do (or not do) certain things.

#### The Approach

So, the requirements outlined approximately 90 actions which were to be logged, which could be grouped into 6 categories (User Actions, Client Actions, etc.). My approach was to give each of the actions a unique ID, and these IDs also indicated a category or "Level". So all of our "User Actions" were "100 Level" actions, for example the "User login successful" message had the ID of 104. Anything to do with "Client Actions" (such as "Client updated") would get a "200 Level" ID (such as 202). This means our IDs actually tell us two things, the specific action being logged and the "Level" or category; this will come into play later.

Since all of this logging is (mostly) taking place in the controllers (but also a couple of instances within the models), I figured a reasonable approach would be a trait which could be used on multiple classes (subsequently, most of the controllers in this case actually inherit from a custom controller class, which just made it that much sweeter and quicker to implement by simply using the trait on the inherited class). So with one trait I was able to add methods that implemented and extended the Monolog logger.

While logging a timestamp and action name is nice, it would be even better to have some context as well. Thankfully, Monolog already has this built in (it writes context information as a JSON string into the log file, which for this client was perfectly fine), so it was just a matter of populating that context with some... context. To start, there is always going to be a couple of pieces of info I want to collect, namely the IP address and the class used to log the action (the class part became a bit redundant in _most_ cases after implementing the ID system, but it still may prove useful in the future as it will tell anyone reviewing the logs the exact class to jump to). Aside from that, I built my custom methods to accept an array of context items which would get added to the IP and class.

The last step is that very granular control and this is where environment variables come into play. First off, I created an environment variable to tell my trait where to tell Monolog to log the actions. But to add more control I utilized environment variables to tell my trait to:

1. disable logging for a specific action
2. disable logging for an entire "Level"
3. specify a specific log file for an entire "Level"
4. specify a specific log file for a specific action

#### The Execution

First things first, I needed to add Monolog by adding it to my composer.json file:

```json
{
    "monolog/monolog": "^1.17"
}
```

Once that was installed it was time to create my trait.

```php
<?php

namespace MyApp;

use \Monolog\Logger;
use \Monolog\Handler\StreamHandler;

trait MyLogger{

    public function log($stream, $id){
        $log = new Logger($stream);
        $log->pushHandler(new StreamHandler($this->log_file($id), Logger::DEBUG));
        return $log;
    }

    public function add_context($context){
        $context['IP'] = \Request::ip();
        $context['controller'] = get_class($this);
        return $context;
    }

    public function log_error($id, $message, $context = [], $stream = 'mystream'){
        if(!$this->skip($id)){
            $log = $this->log($stream, $id);
            $context = $this->add_context($context);
            $message = "$id: $message";
            $log->addError($message, $context);
        }
    }

    public function log_warning($id, $message, $context = [], $stream = 'mystream'){
        if(!$this->skip($id)){
            $log = $this->log($stream, $id);
            $context = $this->add_context($context);
            $message = "$id: $message";
            $log->warning($message, $context);
        }
    }

    public function log_info($id, $message, $context = [], $stream = 'mystream'){
        if(!$this->skip($id)){
            $log = $this->log($stream, $id);
            $context = $this->add_context($context);
            $message = "$id: $message";
            $log->addInfo($message, $context);
        }
    }

    public function skip($id){
        if(env('DISABLE_LOG_' . $id))
            return true;

        $level = floor($id / 100)*100;

        if(env('DISABLE_LOG_LEVEL_' . $level))
            return true;

        return false;
    }

    public function log_file($id){
        $log_file = env('ACTIVITY_LOG');

        $level = floor($id / 100)*100;

        if(env('ACTIVITY_LOG_LEVEL_' . $level))
            $log_file = env('ACTIVITY_LOG_LEVEL_' . $level);

        if(env('ACTIVITY_LOG_' . $id))
            $log_file = env('ACTIVITY_LOG_' . $id);

        return $log_file;
    }
}
```

So as you can see there are a handful of methods here, let's break them down:

1. `log` - this is literally just getting an instance of our Monolog logger class (and setting up the handler). This never really needs to be called by anything other than our `log_*` functions
2. `add_context` - as mentioned earlier, this takes the passed in context array and appends the IP and class. This gets called from each of the `log_*` methods and, again doesn't really need to be called from anywhere else.
3. The `log_*` functions - specifically `log_error`, `log_warning`, and `log_info`. These functions all do basically the same thing, get a log object (from `log`), add default context to what's passed in (with `add_context`), compose the actual message, and write it to the actual log using the appropriate method provided from the Monolog class.
4. `skip` - This is the function that determines if the specific message should be logged. It first checks to see if there is an environment variable to tell us to *not* log this item, if no such variable exists it checks for another to see if the entire level is to be disabled (`DISABLE_LOG_*` and `DISABLE_LOG_LEVEL_*` respectively). This gets called from all of our `log_*` methods.
5. `log_file` - finally the method that tells us what file to log our action to, it uses a similar approach to `skip`, it first checks for a generic log file, then for one specific to the Level of the action and finally one specific to the action itself (`ACTIVITY_LOG`, `ACTIVITY_LOG_LEVEL_*`, and `ACTIVITY_LOG_*` respectively).

Here's an example of what some of these environment variables might look like.

```env
ACTIVITY_LOG=/var/log/my_app.log

ACTIVITY_LOG_LEVEL_100=/var/log/my_app.100.log

ACTIVITY_LOG_110=/var/log/my_app.110.log
ACTIVITY_LOG_111=/var/log/my_app.110.log
ACTIVITY_LOG_112=/var/log/my_app.110.log
ACTIVITY_LOG_113=/var/log/my_app.110.log
ACTIVITY_LOG_114=/var/log/my_app.110.log
ACTIVITY_LOG_115=/var/log/my_app.110.log

DISABLE_LOG_116=true
DISABLE_LOG_LEVEL_200=true
```

In the above you can see a few things.

1. *Most* items are getting to logged to `/var/log/my_app.log`
2. *Most* 100 level items are getting logged to `/var/log/my_app.100.log`
3. Items `110` to `115` inclusive are getting logged to `/var/log/my_app.110.log`
4. The previous two facts tell us that something like `134` is getting logged to `/var/log/my_app.100.log` as well
5. Item `116` is not getting logged
6. **None** of the `200` level items are getting logged (`200`, `202`, `216`, etc.)

#### The Implementation

Finally I implemented the trait into an inherited class (a custom class that was already extending the base Controller which was implemented by many of the custom controllers anyways) and any other classes that may need logging. First, adding the trait:

```php
<?php
namespace MyApp\Http\Controllers;

use MyApp\Http\Controllers\Controller;
[...]
use Illuminate\Http\Request;

use MyApp\MyLogger;

class CustomController extends Controller
{
    use MyLogger;
    [...]
}
```

And finally, calling a `log_*` methods from within a controller.

```php
<?php

namespace MyApp\Http\Controllers\Api;

use MyApp\Http\Controllers\CustomController;
[...]
use Carbon\Carbon;

class PackageController extends CustomController
{
    [...]
    public function show( $id ){
        [...]
        //This line injected to do logging, nothing else in the controller has been disturbed
        $this->log_info(140, 'User viewing package',
            ['email' => $user->email, 'package_id' => $package->id]);
        return Response::json( $package );
    }
    [...]
}
```

And there you have it. If you're interested in this type of logging and are using Laravel or at least PHP, I would recommend giving Monolog a look. And if you need the granular control and clean implementation I've outlined, hopefully this will serve you as a jumping off point.

Happy Coding
