# Introduction

## Pre-requisites

This tutorial assumes a basic familiarity with Ruby on Rails, the UNIX or OS X command line, and that you have at least deployed an application to a service such as Heroku.

## Sample Rails Application

Please download this [sample Rails application](https://github.com/nellshamrell/widgetworld) - this is the application we will configure and deploy.

You will also need an Ubuntu 14.04 Virtual Machine, see the next section for information on how to get one.

## Getting a VM

### Getting a Local VM

My favorite way to run a local VM is using [Vagrant](https://www.vagrantup.com/) and [Virtual Box](https://www.virtualbox.org/)

Follow the Vagrant setup and installation instructions located [here](https://docs.vagrantup.com/v2/getting-started/index.html)

After you have familiarized yourself with Vagrant following those instructions, create a new directory for this tutorial
```bash
mkdir ops_for_developers
```

The navigate into that directory.

```bash
cd ops_for_developers
```

Once there, create a new Vagrant file - this time for an Ubuntu 14.04 server

```bash
vagrant init ubuntu/trusty64 
```

Then start your new virtual server.  The first time you run this command it will take a little bit and you will see lots of output.
```bash
vagrant up
```

Once this is complete, ssh into your new virtual server with 
```bash
vagrant ssh
```

### Getting a Cloud VM

TODO - Add instructions for obtaining and AWS VM, Azure VM, etc.
