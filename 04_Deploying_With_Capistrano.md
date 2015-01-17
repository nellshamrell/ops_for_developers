## 1. Set Up Capistrano

Capistrano lives on the local machine you deploy from rather than on the VM itself.  We will be using Capistrano 3 (NOTE: this version of Capistrano is very different from previous versions, be sure you are using version 3!)

If you haven't already, please clone this [sample Rails application](https://github.com/nellshamrell/widgetworld) onto your LOCAL machine, this is the application we will use Capistrano to deploy to our VM.

```bash
(Local) $ git clone git@github.com:nellshamrell/widgetworld.git
```

Change into the directory where you cloned this application.

i.e. if you cloned the Rails app into a directory called "widgetworld"

```bash
(Local) $ cd widgetworld
```
Next, let's install Capistrano and some helpful other gems.

In your Gemfile, add these lines

```bash
group :development do
  gem 'capistrano',  '~> 3.1'
  gem 'capistrano-rails', '~> 1.1'
  gem 'capistrano-rvm'
end
```

Then run
```bash
(Local) $ bundle
```

There is also some special additional installation for capistrano.  Run this command, which will add some additional Capistrano config files.
```bash
(Local) $ cap install
```

Next, open your Capfile in your favorite editor

```bash
(Local) $ vi Capfile
```

Uncomment this line

```bash
# require 'capistrano/rvm'
```

So it looks like this

```bash
 require 'capistrano/rvm'
```

Later on we'll be storing our database credentials (including password) in config/database.yml.  To avoid accidentally committing this to our potentially public repo, let's first make a copy of the file to serve as an example

```bash
(Local) $ cp config/database.yml config/database_example.yml
```

The open up your .gitignore file with your favorite editor

```bash
(Local) $ vi .gitignore
```

And add this line to the file

```bash
config/database.yml
```

This will keep the database.yml file out of any git commits, histories, or repos.

Next, open up the config/deploy.rb file with your favorite text editor

```bash
(Local) $ vi config/deploy.rb
```

Change these to lines
```bash
set :application, 'my_app_name'
set :repo_url, 'git@example.com:me/my_repo.git'
```

To this

```bash
set :application, 'widgetworld'
set :repo_url, 'git@github.com:nellshamrell/widgetworld.git'
```

Then change this line
```bash
# set :deploy_to, '/var/www/my_app_name'
```

To this

```bash
 set :deploy_to, '/var/www/widgetworld'
```

Then uncomment this line

```bash
set :scm, :git
```

And uncomment this line

```bash
set :linked_files, fetch(:linked_files, []).push('config/database.yml')
```

Finally, uncomment this line
```bash
 set :linked_dirs, fetch(:linked_dirs, []).push('bin', 'log', 'tmp/pids', 'tm    p/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')
```

And add this sequence of lines right after "namespace :deploy do"
```bash
namespace :deploy do


  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
       # This restarts Passenger
       execute :mkdir, '-p', "#{ release_path }/tmp"
       execute :touch, release_path.join('tmp/restart.txt')
    end
  end

  after :publishing, :restart
```

In the changes to config/deploy.rb we specified that we would be deploying our application to a directory in /var/www on the VM.  Since the deploy user will be doing the actual deploy, let's change the owner of this directory to deploy

```bash
(VM) $ sudo chown deploy:deploy /var/www
```

Finally, we need to make some changes to config/deploy/production.rb (back on your local machine)

Change these lines

```bash
  role :app, %w{deploy@example.com}
  role :web, %w{deploy@example.com}
  role :db,  %w{deploy@example.com}
```

To this

```bash
  role :app, %w{deploy@192.168.33.10}
  role :web, %w{deploy@192.168.33.10}
  role :db,  %w{deploy@192.168.33.10}
```

And change this line

```bash
server 'example.com', user: 'deploy', roles: %w{web app}, my_property: :my_value
```

To this:

```bash
server '192.168.33.10', user: 'deploy', roles: %w{web app}, my_property: :my_value
```

Remember how we added config/database.yml to .gitignore above so it wouldn't be committed or deployed?  Now we need to create this file on the VM itself.


To check whether the application is ready to be deployed with capistrano, run

```bash
(Local) $ cap production deploy:check
```

It will likely error out the first time, telling you that /var/www/widgetworld/shared/config/database.yml does not exist on the VM.  To fix this:

Create and open this file on your VM.
```bash
(VM) $ vim /var/www/widgetworld/shared/config/database.yml
```

Then copy the contents from config/database.yml in the widgetworld directory on your local machine, then paste them into this file on your VM.

You'll need to make some changes under the 'production' section of database.yml on your VM.

Change the username to "deploy" and the password to whatever password you set for the deploy postgres user when you created it.

It should look like this:

```bash
production:
  <<: *default
  database: widgetworld_production
  username: deploy
  password: #{password you set for the deploy user of postgres}
```

Save and close the file.

Run the check again.
```bash
(Local) $ cap production deploy:check
```

If it does not return an error and exits with a line similar to:

```bash
DEBUG [7f34dcb9] Finished in 0.004 seconds with exit status 0 (successful).
```

Then we're ready to deploy!

## 2. Deploying with Capistrano

Deploy with

```bash
(Local) $ cap production deploy
```

Now, navigate to the site's folder on your VM.

```bash
(VM) $ cd /var/www/widgetworld/current
```

And run bundler in this directory.

```bash
(VM) $ bundle
```

Finally, we need to make Apache aware of our new site.  Add this to
/etc/apache2/apache2.conf in your VM

```bash
<VirtualHost *:80>
ServerName 192.168.33.10
DocumentRoot /var/www/widgetworld/current/public
<Directory /var/www/widgetworld/current/public>
# This relaxes Apache security settings.
AllowOverride all
# MultiViews must be turned off.
Options -MultiViews
</Directory>
</VirtualHost>
```

And restart Apache
```bash
(VM) $ sudo service apache2 restart
```

Navigate back to your site in your browser.

If you receive a passenger error "Could not find a JavaScript runtime", run
```bash
(VM) $ sudo apt-get install nodejs
```

The restart Apache again
```bash
(VM) $ sudo service apache2 restart
```

TO DO:

This is erroring out with this error in the apache logs
App 16460 stderr: [ 2015-01-17 18:31:39.4816 16546/0x0000000231cc78(Worker 1) utils.rb:84 ]: *** Exception RuntimeError in Rack application object (Missing `secret_key_base` for 'production' environment, set this value in `config/secrets.yml`) (process 16546, thread 0x0000000231cc78(Worker 1)):

Will keep working at it.
