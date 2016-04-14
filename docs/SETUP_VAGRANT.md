# Setup with Vagrant

## Install [Vagrant](http://www.vagrantup.com/)

Get the installer for your platform at [http://www.vagrantup.com/downloads.html](http://www.vagrantup.com/downloads.html).

## Install [VirtualBox](https://www.virtualbox.org)

Get the installer for your platform and at [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads).

## Install [Git](http://git-scm.com)

Important! Ensure that your setting for line endings is set to commit Unix-style line endings.
To make this setting the default you can enter this command:

    git config --global core.autocrlf input

open Git bash and `cd` to the directory you want the project in.

    git clone <this repo>.git
    cd <this project>

## Setup your local Vagrant virtual machine

    bin/boot_vagrant
    vagrant ssh

## App Setup

Now that we have the system setup, let us setup our environment for this application.

- `cd <app name>` # change into the code directory.
- `./bin/setup`
  - This may stop you or bomb out if there are any issues with setup or missing configuration files.
  - Follow the prompts and re-run this command until it says "bin/setup all done!"
- `./bin/rails server` (to start a local development server)
  - You can stop the server by giving the terminal focus and hitting `Control+C`

## Running the tests

- Run the tests with `xvfb-run bin/rake`

xvfb-run is used to start a virtual X server for the headless webkit browser used for cucumber/capybara tests.
