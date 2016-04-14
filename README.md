# Project Name

1-3 sentence description of this app's purpose.

Built by [Company Name] for [Client Name].

[Github] [CodeClimate] [Codeship] [Honeybadger] badges if you got 'em.


## Technical Overview

It's easy to go overboard here, and it is also easy for this info to become out-of-date.
I recommend omitting things that can be found out by viewing the `Gemfile`. Pretend you are telling another developer you just inherited an app, and they have casually asked you "Really, what's that built with? What version?", or omit it entirely.

- Ruby version 2.x (see `.ruby-version` file for exact version)
- Rails version 4.x (see `Gemfile` for exact version)
- Database engine: PostgreSQL
- Configuration: `config/database.yml`, `config/application.yml`, and `.env`
- Template Engine: Slim
- Front-end Framework: Bootstrap 3.x


## Getting Started

We will need a few things before we even get started. On a mac with development tools installed, this should get you going:

```shell
$ brew install postgresql # if not installed already
$ git clone <this repository>
$ cd <this repository name>
$ bin/setup # and follow instructions
```

The configuration files that are copied over should be filled in from a trusted source and/or your own computer's configuration.

```shell
$ bin/rails server
```

More detailed setup instructions for OSX are in the file [/docs/SETUP_OSX.md](/docs/SETUP_OSX.md)
Multi-platform setup instructions using a linux virtual machine with Vagrant are in the file [/docs/SETUP_VAGRANT.md](/docs/SETUP_VAGRANT.md)

## Git Workflow

Git workflow is documented in the file [/docs/GIT_WORKFLOW.md](/docs/GIT_WORKFLOW.md)

## Deploy to [staging](http://staging-url.example.com)
One time only: Add git remote for heroku and pull down "staging" branch

```shell
git remote add staging git@heroku.com:appname-staging.git
git checkout --track origin/staging
```

Every time: Push to staging, update db, restart app, tag release, update remote repo, switch back to master

```shell
git checkout master
git pull
git checkout staging
git pull
git merge master
git push staging staging:master
heroku run bundle exec rake db:migrate --app appname-staging
heroku run rake translations:import --app appname-staging
heroku restart --app appname-staging
git tag `date +"%Y%m%d"`-`git rev-parse --short HEAD`
git push
git push --tags
git checkout master
```


## Deploy to [production](http://production-url.example.com)
One time only: Add git remote for heroku and pull down "production" branch

```shell
git remote add production git@heroku.com:appname-production.git
git checkout --track origin/production
```

Every time: Check out production branch, merge staging, make db backup, deploy, update db, restart app, tag release, update remote git host

```shell
git checkout staging
git pull
git checkout production
git pull
git merge staging # or 'git merge master' if you didn't deploy first to staging
heroku pg:backups capture --app appname-production # snapshot the db
git push production production:master # this pushes the "production" branch as "master" to Heroku
heroku run bundle exec rake db:migrate --app appname-production
heroku run rake translations:import --app appname-production
heroku restart --app appname-production
git tag `date +"%Y%m%d"`-`git rev-parse --short HEAD` # don't worry if this complains about it already existing
git push
git push --tags
git checkout master # switch back to master branch
```

## Notes & Tips

### See what version is in production

```shell
git ls-remote production
heroku run bundle exec rake db:version --app appname-production
```

### Download the latest backup to your local db

```shell
curl -o db/latest.dump `heroku pg:backups public-url --app appname-production`
pg_restore --verbose --clean --no-acl --no-owner -h localhost -U `whoami` -d appname_development db/latest.dump
```

### Copy the production database to staging

```shell
heroku pg:copy appname-production::DATABASE_URL DATABASE_URL --app appname-staging
```
