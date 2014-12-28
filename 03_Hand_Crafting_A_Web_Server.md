For this lesson, you should be ssh'd into your Virtual Machine (Virtual Machine) you created in the previous lesson.

We will now be running commands both on your VM and local machine.

If you need to run a command on your VM, it will be denoted like this:

```bash
(VM) $ cd this_directory
```

If you need to run a command on your local machine, it will be denoted like this

```bash
(Local) $ cd this_directory
```
## 1. Create a deploy user

You will be prompted to set a password for this user.  Record this for reference later.

```bash
(VM) $ sudo adduser deploy
```

Add this user to the sudo users group.  This will allow the user to use sudo to run commands.

```bash
(VM) $ sudo adduser deploy sudo
```

Switch to the deploy user account.

```bash
(VM) $ su deploy
```
## 2. Install SSH

We will need to be ssh into the server as the deploy user.  In order to do this, we need to install OpenSSH Server on the VM.

```bash
(VM) $ sudo apt-get install openssh-server
```

On your local machine, find your public ssh key by running

```bash
(Local) $ cat ~/.ssh/your_public_key.pub
```

Copy this to your clipboard.

Back in your VM, create an ssh directory

```bash
(VM) $ mkdir ~/.ssh
```

Create an authorized_keys file in the ssh directory using whichever editor you prefer (I'm a vi/vim user myself)

```bash
(VM) $ sudo vi ~/.ssh/authorized_keys
```

Paste the ssh key from your local machine into this file.  Save and quit the file.

Now it's time to try ssh'ing out the VM as the deploy user.  Follow the appropriate steps below.

### With Vagrant

Leave your vagrant machine running, but open a new terminal shell on your local machine.

SSH into your VM from the new shell as the deploy user following the appropriate guidelines below.

Vagrant VMs are a little complicated to ssh into outside of the normal vagrant commands.  In the same directory as your Vagrant file run:

```bash
(local) vagrant ssh-config > vagrant-ssh
```

Open vagrant ssh with your favorite text editor.  My favorite is vi/vim.

```bash
(local) vim vagrant-ssh
```

Change the User to deploy then change the IdentityFile to the path to your private ssh key (NOT the key itself, just the path).

Next, ssh into your VM
```bash
(local) ssh -F vagrant-ssh default
```

### With AWS

### With Azure

## 3. Install Ruby

In order to install Ruby on our VM, we first need to add some packages to the systems.

First, update Ubuntu itself by running
 
TODO: Troubleshoot why sudo is still requiring password - check info at https://help.ubuntu.com/10.04/serverguide/openssh-server.html
```bash
(VM) sudo apt-get update
```

Then add in some dependencies for Ruby

```bash
(VM) sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties
```

There are a few different ways to install Ruby.  For this tutorial we're going to use [RVM](https://rvm.io/)




