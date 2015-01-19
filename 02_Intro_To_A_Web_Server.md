If you haven't already, ssh to your VM as root.

## Ubuntu

Ubuntu is a commonly used both as a desktop and as a server platform.  For a thorough introduction to Ubuntu Server, I recommend the [Ubuntu Server Book](http://www.amazon.com/Official-Ubuntu-Server-Book-Edition/dp/0133017532#).  Now that you're ssh'd into the VM, let's take a look at what has been installed with Ubuntu 14.04.

```bash
(VM) $ ls /
```

This will show you a list of directories that were created when you installed Ubuntu.  Let's take a look at the directories we will use in this curriculum.

### etc
Both your server and *services* that run on your server require config files.  You can alter these files to change how your servers or services run.  This directory is where those config files live.  (Source: [Ubuntu Server Book](http://www.amazon.com/Official-Ubuntu-Server-Book-Edition/dp/0133017532#))

### var
This directory stores files and directories who's sizes vary (hence the "var" name of the directory).  This includes all system logs and web server documents.  Of particular note:
* /var/log stores all system logs.  If a service is returning errors, this is a good place to look for the log that will help you troubleshoot.
* /var/www stores all web documents if your server is a web server.  Our Rails app will live in this directory when we deploy it.
(Source: [Ubuntu Server Book](http://www.amazon.com/Official-Ubuntu-Server-Book-Edition/dp/0133017532#))

## Parts of a Rails Web Server

There are many types of servers that can be run with Ubuntu (DNS servers, web servers, mail servers, etc.).

In this tutorial we will be configuring an Ubuntu *web server*.  There are three main parts that make up a server which can run a Rails application.  We will configure each of these parts shortly.  For now, here is a brief intro to each piece:

### Web Server
TODO - Brief explanation
In this tutorial, we will be using Apache.

### Application Server
TODO - Brief explanation
In this tutorial, we will be using Passenger.

### Database
TODO - Brief explanation
In this tutorial, we will be using PostgreSQL
  
