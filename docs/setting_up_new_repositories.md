# Setting up new repositories

We use a multitude of different tools to automate some parts of the work we regularly do on our projects. This document is supposed to guide through the necessary setup so that the repository fits nicely into the workflow we already use for other projects.

1. Ask a team member with admin rights to create the repository.
1. Create the issue labels that we use for our review workflow. Have a look at our [other repositories](https://github.com/liefery) to see which labels we are using.
1. If the project requires some dependencies that should be kept up-to-date, ask a team member with admin rights to enable the [Depfu](https://depfu.com/) integration for the repository.
1. Setup CI. If the repository is public, ask a team member with admin access to enable the [Travis CI](https://travis-ci.com/) integration for the repository. If the repository is private, [setup CI with Jenkins](/setting_up_ci_for_new_projects.md).
1. [Setup our pull request merge bot](https://github.com/liefery/pr-merge-bot#adding-the-merge-bot-to-a-new-repository).
1. If you want to separate the tickets that are linked to this project from tickets that are linked to our other projects, ask a team lead to create a new Jira component to organize the work on this project.
1. [Setup our GitHub-Jira-Bot](https://github.com/liefery/github-jira-bot#adding-the-bot-to-a-repository)
1. If you want to automate deployments (e.g. regular staging deployments or production deployments through Slack), have a look at [setting up Deployer](https://github.com/liefery/deployer#adding-a-new-app-stage)
