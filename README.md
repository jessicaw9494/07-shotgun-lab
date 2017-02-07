# Using the Shotgun Development Server

## Overview

This lesson will introduce you to shotgun and how to use it with Sinatra apps. We'll also cover troubleshooting common problems that you might encounter when running `shotgun`. 

## Objectives

1. Explain how using `rackup` to start a Sinatra application will only read the code once at boot 
2. Describe how `shotgun` allows for automatic code reloading
3. Install shotgun and require it in an application's `Gemfile` 
4. Start and stop a rack or Sinatra application with shotgun
5. Troubleshoot common `shotgun` errors including "command not found, "bundler error", and "port in use."

## Setup

In the last lab, we used `ruby app.rb -p $PORT -o $IP` to run a single file.

But an app is more than a single file, so we need to use the `rackup` command which uses **rack** to load the entire MVC structure.  Try doing `rackup app.rb -p $PORT -o $IP`


You _should_ get an error:
```
...
Run `bundle install` to install missing gems.
```

This is a tip that you will get every once in a while.  You'll notice that this lab contains a `Gemfile`: it just lists the requires gems (pre-written code) in order to run.  Like the tip says, run `bundle install` to install missing gems.  It wouldn't be a bad idea to memorize this command.  

> If you get an error, then you need to `gem install bundler` then try again.

Now try `rackup app.rb -p $PORT -o $IP` again.

## Why shotgun

In cloud9, you might not get the notification that your app is now running, but you can always get to it from the Menu bar at the top: `Preview` > `Preview Running Application`.  You should see `Welcome to your app!!!!` on the screen.

When starting an application with `rackup`, our application code is read once on boot and never again. Once we start the application locally, if we make changes to our code, our running application server will not read those changes until it is stopped and restarted.

Now add some more text to the string in the controller action:

File: `app.rb`
```ruby
  get '/' do 
    "Welcome to your app!!!! I BUILT THIS!"
  end
```
Refresh the page in the browser. You should just see `Welcome to your app!!!!`. That's because rack isn't aware that we made changes. You can shut down your server by going back to terminal and hitting `ctrl` + `c`. 

Start your server back up by entering `rackup app.rb -p $PORT -o $IP` and now try reloading the preview. It should work and you should see the text `Welcome to your app!!!! I BUILT THIS!` in your browser window.

This tedious save, stop, and restart cycle makes developing Rack or Sinatra applications near impossible. To avoid this, instead of starting our application with `rackup`, we will use `shotgun`.

[Shotgun](https://github.com/rtomayko/shotgun) is a small Ruby gem that makes it easier to develop Rack-based Ruby web applications locally by starting Rack with automatic code reloading. A gem is just a library of code that developers wrote and made free and available to the public. This gem lets us start Rack to have a development server running to test our app.

When you start an application with `shotgun`, all of your application code will be reloaded upon every request. That means if you change anything in your code and save it, when you hit 'Refresh' in your browser, your application will respond with the latest version of your code.

## Installing shotgun

You can install shotgun via `gem install shotgun`. You should also require it in an application's `Gemfile` so that you can install it and load it via Bundler. Shotgun is already in your gemfile, so go ahead and enter `bundle install` in terminal if you haven't already.

## Starting and Stopping shotgun

Within a rack or Sinatra application directory, you can start the application via shotgun by simply executing `shotgun -p $PORT -o $IP` in your terminal. You should see something like:

```
$ shotgun -p $PORT -o $IP
== Shotgun/Thin on http://0.0.0.0:8080/
Thin web server (v1.6.4 codename Gob Bluth)
Maximum connections set to 1024
Listening on 0.0.0.0:8080, CTRL+C to stop
```

Now, with your shotgun server still running, change the text in the string in the controller action:

```ruby
get '/' do
  "Started my server using shotgun!"
end
```

Save your changes to `app.rb` and visit your running app again in the browser. Hit 'Refresh' in your browser to make a new request. This time, you should see the text `Started my server using shotgun!` as opposed to seeing the previous text, just like with rackup.

## Troubleshooting Shotgun

### command not found

When you run your `shotgun` command from your terminal you might get a Shell error that reads something like:

```
$ shotgun -p $PORT -o $IP
-bash: shotgun: command not found
```

That means the shotgun gem isn't properly installed or available in your PATH. Try fixing it with:

```
$ gem install shotgun
```

If you still get that error, from within your application directory, try running:

```
$ bundle install
```

followed by

```
$ bundle exec shotgun
```

### bundler error

You might get an error about `bundler` that will tell you to run `bundle install`. 
It'll look like this:

```
$ shotgun -p $PORT -o $IP
bundler: command not found: shotgun
Install missing gem executables with `bundle install`
```

Just run `bundle install` and try your `shotgun` command again. If you still get the error try running via:

```
$ bundle exec shotgun
```

### port in use

You also might get an error about a port being in use. It'll look like this:

```
$ shotgun -p $PORT -o $IP
== Shotgun/Thin on http://127.0.0.1:9393/
Thin web server (v1.6.3 codename Protein Powder)
Maximum connections set to 1024
Listening on 127.0.0.1:9393, CTRL+C to stop
/Users/avi/.rvm/gems/ruby-2.2.2/gems/eventmachine-1.0.8/lib/eventmachine.rb:534:in `start_tcp_server': no acceptor (port is in use or requires root privileges) (RuntimeError)
```

This means you have another shotgun server running somewhere. Do you have another Terminal or Shell open running a web application or shotgun? You need to find that process or tab that is running a web application using shotgun and shut it down with `CTRL+C`.