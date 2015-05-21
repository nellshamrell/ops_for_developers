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

Next, let's allow the deploy user to use sudo without be prompted for a password.  A great way to do this with recent versions of sudo is to create a sudo config file for the deploy user.

Create the file with your text editor of choice (here I use vi)

```bash
(VM) $ sudo vi /etc/sudoers.d/deploy
```

Add this line to the file.

```bash
deploy ALL=(ALL) NOPASSWD:ALL
```
Then save and quit the file.

And, finally, restart the sudo service so the changes go into affect (yes, we're using sudo to restart the sudo service)

```bash
(VM) $ sudo service sudo restart
```

Switch to the deploy user account (you will be prompted for the password you entered for the deploy user above).

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

### SSHing with Vagrant

Leave your vagrant machine running, but open a new terminal shell on your local machine.

SSH into your VM from the new shell as the deploy user following the appropriate guidelines below.

Vagrant VMs are a little complicated to ssh into outside of the normal vagrant commands.  In the same directory as your Vagrant file (but NOT within the Vagrant machine itself) run:

```bash
(local) $ vagrant ssh-config > vagrant-ssh
```

Open vagrant ssh with your favorite text editor.  My favorite is vi/vim.

```bash
(local) $ vim vagrant-ssh
```

Change this

```bash
User vagrant
```

To this

```bash
User deploy
```

Then change this line
```bash
IdentityFile /Users/path/to/vagrant/key
```

To this - remember, include only the path to the file containing your private key, NOT the contents of the key itself!
```bash
IdentityFile path/to/local/private/rsa/key
```

Save and quit the file.

Next, ssh into your VM
```bash
(local) $ ssh -F vagrant-ssh default
```

### With AWS

### With Azure

### Securing SSH

Now that SSH is installed, there are few needed steps to add security to the VM.

First, on your VM, edit the sshd_config with your editor of choice (here I use vi)

```bash
(VM) $ sudo vi /etc/ssh/sshd_config
```

First, to disable root login via ssh change this line
```bash
PermitRootLogin without-password
```
to this
```bash
PermitRootLogin no
```

Then, to disable password authentication, change this line
```bash
PasswordAuthentication yes
```

to this
```bash
PasswordAuthentication no
```

Save and quit the file, then restart the ssh service to load the changes

```bash
(VM) $ sudo /etc/init.d/ssh restart
```

## 3. Install Ruby

In order to install Ruby on our VM, we first need to add some packages to the systems.

### Updating and Installing Dependencies

First, update Ubuntu itself by running

```bash
(VM) $ sudo apt-get update
```

Then add in some dependencies for Ruby

```bash
(VM) $ sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties
```

There are a few different ways to install Ruby.  For this tutorial we're going to use [RVM](https://rvm.io/)

### Installing RVM

First, install some dependencies required by RVM
```bash
(VM) $ sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
```

Next, retrieve RVM and install

```bash
(VM) $ curl -L https://get.rvm.io | bash -s stable
```

NOTE: If you receive an error "gpg: Can't check signature: public key not found", run this command to install the required key
```bash
(VM) $ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

Then re-run

```bash
(VM) $ curl -L https://get.rvm.io | bash -s stable
```

### Sourcing RVM
Next, we need to source the rvm scripts directory and add it to the VM's .bashrc

```bash
(VM) $ source ~/.rvm/scripts/rvm
```

```bash
(VM) $ echo "source ~/.rvm/scripts/rvm" >> ~/.bashrc
```

### Installing Ruby

First, we install Ruby 2.1.3
```bash
(VM) $ rvm install 2.1.3
```
NOTE- don't worry if you see the message "Unknown ruby string (do not know how to handle): ruby-2.1.3."  Wait a few seconds and see if the install continues, you should see the message "Searching for binary rubies, this might take some time."

Next, we tell RVM to use 2.1.3 as the default Ruby
```bash
(VM) $ rvm use 2.1.3 --default
```

And, finally, it's time to check the Ruby version.
```bash
(VM) $ ruby -v
```

If everything is set up correctly, you should see this when you run the previous command
```bash
(VM) $ ruby -v
ruby 2.1.3p242 (2014-09-19 revision 47630) [x86_64-linux]
```

## 4. Install Apache
```bash
(VM) $ sudo apt-get install apache2
```
Next, we need to verify that Apache is working on the VM.  To do this from the command line, run

```bash
(VM) $ wget -qO- 127.0.0.1
```

If Apache is installed correctly, the command line will output an html document which includes the words "It works!"

Now to verify that the VM can be accessed through a web browser.

### If you are using Vagrant

At this point, even with Apache installed, we cannot access the VM in the browser.

To fix this, open up your VagrantFile on your local machine with the editor of your choice (I use Vim)

```bash
(Local) $ vim Vagrantfile
```

Locate this line in the Vagrantfile
```bash
# config.vm.network "private_network", ip: "192.168.33.10"
```

Uncomment out that line so it looks like this
```bash
 config.vm.network "private_network", ip: "192.168.33.10"
```

This assigns an IP address to our Vagrant machine so we can access it in the web browser.

Then save and quit the file.

If your VM is still running, run this command to reload the configuration to include these changes.
```bash
$ (Local) vagrant reload
```

Now, access "http://192.168.33.10" in a browser and you should see the Apache2 Ubuntu Default Page.  It's a working web server!

## 5. Install Passenger

Switch back to your VM.  First, download and install the Passenger gem
```bash
(VM) $ gem install passenger
```

Use this command to set up Passenger
```bash
(VM) $ passenger-install-apache2-module
```

This will guide you through the set up.
Make sure to select Ruby as a language you prefer.

If the installer advises you to install additional packages, go ahead and install them.  Then rerun
```bash
(VM) $ passenger-install-apache2-module
```

If the installer advises you to add more swap memory, follow the directions to do this and re-run the installer again.

Eventually, the installer will prompt you to add these lines to your Apache configuration file.  Paste these into /etc/apache2/apache2.conf

```bash
   LoadModule passenger_module /home/deploy/.rvm/gems/ruby-2.1.3/gems/passenger-4.0.57/buildout/apache2/mod_passenger.so
   <IfModule mod_passenger.c>
     PassengerRoot /home/deploy/.rvm/gems/ruby-2.1.3/gems/passenger-4.0.57
     PassengerDefaultRuby /home/deploy/.rvm/gems/ruby-2.1.3/wrappers/ruby
   </IfModule>
```

Then restart Apache2
```bash
(VM) $ sudo service apache2 restart
```
Finally, verify that passenger is running with this command:
```bash
(VM) $ passenger-memory-stats
```

You should see a list of processes.

## 6. Install PostgreSQL

Install PostgreSQL with:
```bash
(VM) $ sudo apt-get install postgresql
```

By default, PostgreSQL will create a postgres user.  Let's switch to this user.

```bash
(VM) $ sudo -u postgres -s
```

Rather than using the postgres user all the time, we're going to create a deploy user our application will use to connect to the database.

```bash
(VM) $ createuser deploy --pwprompt
```

Make sure to note the password, you will need it later when configuring your Rails application.

Finally, exit out of the postgres user account.

```bash
(VM) $ exit
```

Now, let's enter into Postgres and give the deploy user we just created permission to create a database.  Start up Postgres with this command:

```bash
(VM) $ sudo -u postgres psql postgres
```

When the psql prompt appears, enter this command:

```bash
(VM) $ ALTER USER deploy CREATEDB;
```


And we now have a basic, working web server.  To learn how to use Capistrano to deploy your application to this web server head on over the the next chapter!
