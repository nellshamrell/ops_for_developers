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

Leave your vagrant machine running, but open a new terminal shell on your local machine.
## 3. Install Ruby
