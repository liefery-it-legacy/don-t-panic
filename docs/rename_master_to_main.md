# Renaming the 'master' to 'main'

We recently collectively decided to rename our master branches to 'main'. For reasoning you can read [here](https://dev.to/_garybell/why-github-renaming-master-is-good-4b64) and also follow up on our meeting discussion [here](https://lieferyit.slack.com/archives/CFGNDBF37/p1592831621009300).

* [Where do we start](#where-do-we-start)
* [What do we need to change?](#what-do-we-need-to-change)
* [Instructions](#instructions)

## Where do we start?

## Smaller Repositories
* [Don't panic](https://github.com/liefery/don-t-panic)
* Deployer
* Jira-Bot

## What do we need to change?
* Remote branch
* On Github configure default branch as 'main' (this can only be done by teamleads)
* Change all links in the Readme and docs files (they often refer to an URL with the master in it)
* If deployed by deployer: [Deployer Changes](#deployer-changes)
* Change column name on Jira
* Change settings on Jenkins

## Instructions

### Renaming the Local master Branch to main

Make sure you are on your local master branch and also pulled the latest changes.
The first step is to rename the "master" branch in your local Git repositories:

```bash
git branch -m master main
```
Check with `git status` if it worked.

### Renaming the Remote master Branch
Check out your local `main` branch with `git checkout main` and then push your branch to the remote repository with
```bash
git push -u origin main
```
Before deleting the remote master branch, you want to go and check for open PRs. Are they all comparing to master? If so, they will automatically be closed once you delete master. But this is no problem, since the working branches are all still there and the history with the closed PRs as well and you can just open new PRs comparing to main for them. Do that manually as you would for a new PR anyway. You can also do this after deleting the remote master branch.

Now remove the old master branch remotely with
```bash
git push origin --delete master
```
Now there could be the following error message coming up:
```bash
To https://github.com/gittower/git-crashcourse.git
! [remote rejected]   master (refusing to delete the current branch: refs/heads/master)
error: failed to push some refs to 'https://example@github.com/gittower/git-crashcourse.git'
```
Because on Github you probably configured the `master` branch as the default branch. To configure the main branch as default, go to the settings/branches and change the default branch to main. Then try deleting master again with the command given above and it should work.

### Team Duties after that
The complicated way:

```bash
# Switch to the "master" branch:
$ git checkout master

# Rename it to "main":
$ git branch -m master main

# Get the latest commits (and branches!) from the remote:
$ git fetch

# Remove the existing tracking connection with "origin/master":
$ git branch --unset-upstream

# Create a new tracking connection with the new "origin/main" branch:
$ git branch -u origin/main
```
The easy way:
```bash
# get the remote main branch and check it out locally
$ git checkout main

# delete local master branch
$ git branch -D master
```

### Deployer Changes / Manual Deployment

## Checking the Github Integration in Deployer

First of all if you go to `deployer/app/services/github_integration.rb`, you will see that there is a list of all the repositories, that have an integration with github and what branch we are using for which stage. Check if you can rename the `master` to `main` here.

## Check Capistrano Deployment (also relevant for Deployer)

Capistrano seems to deploy by default from master. In `deployer/config/deploy.rb` you can find a comment about changing the branch. It will work with
```ruby
ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }
```

or

```ruby
set :branch, proc { `git rev-parse --abbrev-ref main`.chomp }
```

