# Setup on OSX

These should get a fresh OSX install to a state where the application can be run and developed against.

If you run into any issues or notice any discrepancies in these instructions, please update this document.

## Xcode

You'll need Xcode to get the compilers necessary to install databases, packages and gems that we will need later.

- Launch App Store
- Search for and install "Xcode"
- Launch Xcode and step through the setup
- Quit Xcode
- Open `Terminal.app`
- Run `xcode-select --install` and follow the prompts

---

## Homebrew

Homebrew is a package manager for open-source software on OSX. This makes it easy to install system-related dependencies.

- Go to [http://brew.sh/](http://brew.sh/)
- Open `Terminal.app`
- Run the command [here](http://brew.sh/#install) in `Terminal.app`
  - Should look like: `ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"`
- Run `brew update` to get the latest package version info
- Run `brew doctor` to make sure you have all the things necessary for homebrew to work smoothly

---

## Postgres

- From `Terminal.app`, run `brew install postgresql`
- Follow directions from post-install to setup a database and start it on boot (they should be something like this)
  - `initdb /usr/local/var/postgres -E utf8` (to create postgres database)
  - `ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents`
  - `launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist` (to load postgres on boot)

---

## Ruby

OSX comes with a system Ruby installed, but it is best to not tamper with that, and use a Ruby Version Manager.
This installs [chruby](https://github.com/postmodern/chruby) and [ruby-install](https://github.com/postmodern/ruby-install):

- brew install chruby
- brew install ruby-install
- echo 'source /usr/local/opt/chruby/share/chruby/chruby.sh' >> ~/.profile
- echo 'source /usr/local/opt/chruby/share/chruby/auto.sh' >> ~/.profile
- source ~/.profile

---

## App Setup

Now that we have the system setup, let us setup our environment for this application.

- Open `Terminal.app`
- `git clone <this repo>.git`
- `cd <this project name>`
  - This may prompt `chruby` to notify you install a Ruby based on the contents of the `.ruby-version` file in the repository. Do this if necessary.
  - `ruby-install ruby 2.1.2` # and restart `Terminal.app`, to pick up chruby changes
- `cd launch-vaults` # change back to the code directory.
- `./bin/setup`
  - This may stop you if there are any issues with setup, or missing configuration files.
  - Follow the prompts and re-run this command until it says "bin/setup all done!"
- `./bin/rails server` (to start a local development server)
  - You can stop the server by giving the terminal focus and hitting `Control+C`

## Running the tests

- Run the tests with `bundle exec rake`
