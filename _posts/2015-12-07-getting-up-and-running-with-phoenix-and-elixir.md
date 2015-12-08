---
layout: post
title: Getting up and running with Phoenix and Elixir
---

_Originally posted in [http://tech.findmypast.com/getting-up-and-running-with-phoenix-and-elixir/](http://tech.findmypast.com/getting-up-and-running-with-phoenix-and-elixir/)_

# Getting up and running with Phoenix and Elixir

At FindMyPast we have been trying [Elixir](http://elixir-lang.org/) and [Phoenix](http://www.phoenixframework.org/) for a few months now. After creating some spikes we are about to start creating our first production API.
In this post I want to help you getting up and running with these technologies by using Vagrant.

#### Why Vagrant?

[Vagrant](https://www.vagrantup.com/) lets you create and configure lightweight, reproducible, and portable development environments. The benefits are many but mainly:

- You don't have to download all the packages and dependencies into your machine.

- You can share settings with your colleagues and you can track history of changes with Git as any other file.

- You can use the exact same configuration to run locally than the one used in production.

- All your configuration is in version control

#### What are we going to do?

We are going to create a Linux virtual machine that will run an empty Elixir and Phoenix application. We are going to do this by using Vagrant. 
We will be able to edit the code in our regular machine and the application will be automatically updated.

#### Let's get our hands dirty:

- Download and install [VirtualBox](https://www.virtualbox.org/).

- Download and install [Vagrant](https://www.vagrantup.com/)

- If you are running on Windows, turn off Hyper V feature in Windows features. Also include Vagrant in the System Path variables:

```
setx path "%path%;C:\HashiCorp\Vagrant\bin"
```

- Download the following Vagrant setup files, this includes Postgres, Elixir, Phoenix, and all required dependencies.
[https://github.com/kiere/vagrant-phoenix-postgres](https://github.com/kiere/vagrant-phoenix-postgres)

- Open your favourite command line tool. Navigate to the root folder of the repo you have just downloaded and run the following command:

```
vagrant up
```

This will create a new virtual machine with all required dependencies.
If you get an error message, trying deleting the following code from the VagrantFile and run Vagrant up again:

```
config.vm.synced_folder(
  '.',
  '/vagrant',
  type: 'rsync',
  rsync__exclude: [
    '.git/',
    '.vagrant/',
    'log/*',
    'tmp/'
  ]
)
```

- Run ```vagrant ssh```. That will take you to the actual box you have just created. This machine has all the dependencies installed so that you can write ```iex``` and use the Elixir Interactive tool.

- Vagrant creates a shared folder between your machine and the box. The folder is called _vagrant_.
Let's navigate to it:

```
cd /vagrant
```

- Let's create an empty Phoenix project:

```
mix phoenix.new %name_of_project%
```

This will include live reloading by using brunch. If you don't need it you can run:

```
mix phoenix.new %name_of_project% --no-brunch
```

If you just want an Elixir project you can run:

```
mix new %name_of_project%
```

- Navigate to your new projects folder:

```
cd /%name_of_project%
```

- Run your newly created Phoenix app:

```
mix phoenix.server
```

- In your local machine open up a browser and navigate to [localhost:4000](localhost:4000). You should have the Phoenix application up and running!

From here you can feel free to do changes in the application in the Linux box. In my case I like that since the code is in that shared Vagrant folder with my Windows machine, I can use my regular development tools in my local environment, do changes, and it will be automatically updated in the application.

#### Running tests

```
mix test
```

This command will run all the tests we have with the template.

If you get the following error:
> ** (Mix) The database for Play.Repo couldn't be created, reason given: psql: FATAL:  password authentication fa. FATAL:  password authentication failed for user "postgres"

It means that you need to set a password for the postgres user.

To solve this problem go to postgres:

```
 sudo -u postgres psql
 ```
 
 And once in postgres, run this command:
 
 ```
 alter user postgres with password 'postgres'
 ```

#### Some more useful commands

- If you need to do any changes to the Vagrant configuration, say the port number, just update it the VagrantFile and run:

```
vagrant reload
```

- Once you want to stop the application, first exit the vagrant box ```exit``` and then suspend it ```vagrant suspend```.
Remember not to leave the app running since we are not running the virtual machine from VirtualBox UI and files can be corrupted.
