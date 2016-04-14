# Code contribution workflow

This project uses [git](http://git-scm.com) for version control, and [Heroku](https://heroku.com) for staging and production environments.

---

## Cloning and checking out branches

If you haven't already, you'll need to check out the source code with:

    git clone <this repo>.git

and enter the code directory created:

    cd <this project>

The clone by default will only pull down the `master` branch.

You can see a list of all remote branches on the server with:

    git branch -a

Let's pull down all the remote branches we care about.

    git checkout staging

Then switch back to the master branch, since this is our default "home" branch.

    git checkout master

Because we downloaded this repository from Bitbucket, it automatically set its remote `origin`. See:

    git remote -v # the v is for "verbose"

Let's add some remote destinations for the staging and production environments on Heroku:

    git remote add staging git@heroku.com:<this project name>.git
    git remote -v

Since Heroku uses git-based deployments, to deploy, we simply `push` our repository to Heroku as if it were Bitbucket or Github.

If you haven't actually gotten the the application running with Rails yet, follow the directions in [/docs/SETUP_OSX.md](/docs/SETUP_OSX.md)

---

## Committing code in a feature branch

The "master" branch is the main line of code, it is the working state of the application as being developed. When we want to contribute, we could commit code directly to the master branch, but experience tells us that this really limits our options if we have to pause mid-development (because something else comes up, or we get stuck for a bit, we can always commit the work-in-progress and switch back to master, and come back to this branch later without leaving a half-finished feature in master). The feature branch workflow also allows us to do pull requests for code reviews.

Let's say we have some code we want to add or change.

Start first by making sure we are on the master branch

    git branch

if not, switch to master with:

    git checkout master

Make sure our branch is up-to-date with the remote server:

    git pull

Create a feature branch to do our work in:

    git checkout -b awesome_feature
    git branch # this should show that we are now on the awesome_feature branch

We can then open our text editor and start adding and changing files.

Whenever you have finished, or make enough progress that you want a save-point, add your changes to the git stage and commit them to your branch.

    git add .
    git commit -m "Add awesome feature"

Continue doing this as often as necessary until done with the feature.
When done, we have a choice of merging this into master, or creating a pull request.

### Option 1: Pull Request

This is as simple as pushing your local feature branch to the server:

    git push -u origin awesome_feature

Then going to your branch in Bitbucket and creating a "pull request" from your branch.

    https://bitbucket.org/<org name>/<project_name>/branches

![GitHub Pull Request](/docs/resources/github-pull-request.png)
![Bitbucket Pull Request](/docs/resources/bitbucket-pull-request.png)

This will notify others that this branch should be looked at, and merged into master if acceptable.
If not acceptable, or needs other work done, more commits can be added and pushed to the server until it is, and since your branch is on the server, and accessible by others, they can also contribute.

### Option 2: Merge to master

If you are confident that your changes do not need review, and your tests all pass, you can merge your feature branch back into master like so:

    git checkout master # switch back to master
    git pull # download and apply any commits that may have been added while you were working on awesome_feature
    git merge awesome_feature
    git branch -d awesome_feature # optional, deletes the local branch

This will merge your commits into the master branch.

### Dealing with conflicts

Git is very smart about merging changes from multiple developers, however if multiple people edit the same bit of code, it may not know exactly what to do, and present you with a merge conflict.

The files it cannot automatically merge are changed and lines with `<<<<<<<<<` and `>>>>>>>>>` are inserted showing you both versions of the lines changed. You should be able to delete the one you don't want to keep, and bring the file to a known good state (and re-run the tests). Add the file with `git add .` and commit `git commit -m "Fix merge conflict"`. Or you can abort the whole process with `git merge --abort` or `git reset --hard` (older versions of git) and find some help if necessary.

![Merging and Conflict Resolution](/docs/resources/merging-and-conflict-resolution.png)

### Pushing to staging environment

So `awesome_feature` is now in the master branch, and we want to show it off to the product owner.

We have the `staging` branch for tracking what is deployed on the staging server, let's bring staging up-to-date with `master` and deploy:

    git checkout staging
    git pull # make sure this branch is up-to-date
    git merge master
    git push staging staging:master # this pushes the "staging" branch as "master" to Heroku

Because this is a Rails application, we often need to update our database, so these commands will do that if necessary:

    heroku run bundle exec rake db:migrate --app <your app name>
    heroku restart --app <your app name>

Then a final git push to update `origin` with our updated copy of the `staging` branch.

    git push

### Shipping to production

    git checkout production
    git merge master
    heroku pgbackups:capture --expire --app <your app name> # snapshot the db
    git push production production:master
    heroku run bundle exec rake db:migrate --app <your app name>
    heroku restart --app <your app name>

Then we tag and push the release

    git tag `date +"%Y-%m-%d"`
    git push
    git push --tags

and switch back "home" to the master branch.

    git checkout master

![Development to staging to production](/docs/resources/development-to-staging-to-production.png)

For more information on Git workflows, check out [Git Workflows by Atlassian](https://www.atlassian.com/git/workflows)
