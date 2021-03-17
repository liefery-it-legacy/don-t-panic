# Setting up CI for a new project
We use Jenkins to run builds for all our private repositories for public repositories we use Travis.

## Setting up CI with Jenkins
This guide outlines the necessary steps to set up CI for new private projects.
It considers some steps to already be done, these are:

* Creating the project
* Setting up the repo (If you do not have permissions for that, ask Christian or one of the team leads (Irmela, Adam, Tiago).

### ci-build.sh
This file contains all commands to get your project running as well as commands to run the various test suites you are using.
The ci-build file is responsible for:
* Setting environment variables which are not provided in the Jenkins build steps
* Creating the test databases
* Installing dependencies
* Running linters, unit and integration tests
* Running additional scripts and tests
* Cleaning up after the build (removing unnecessary data that might have been saved during tests)

### Jenkins
We use Jenkins as a tool for most of our CI integrations like running automated builds when updating or creating pull requests as well as running scheduled builds to ensure the system works as expected before deployment.

* To create a new project, go to the [Jenkins Dashboard](https://ci.liefery.com/) and click "New item" in the top left corner.
* Select the project type and give it a name. For most use cases "Freestyle project" is the preferred option.
* In the "General"-tab provide a description, check "Github project" and provide a link to the repository. Here you can also set additional options like "Discard old builds", or whether the project needs lockable resources and much more.
* In "Source Code Management" select Git (we use git as a version control system for all projects), provide a repository url, select "jenkins" for credentials and set the branch specifier to `${sha1}`.(If merge with master is possible `${sha1}` merges master and the PR branch and runs the build on the merged result, otherwise the build runs on the HEAD of the PR-branch. This option should only be used for builds triggered by code changes. )
* Within the "Build trigger"-tab check "Build every pull request automatically without asking". All repos handled by Jenkins are private and we trust the people having access to them to run builds.
* For the basic implementation it's not necessary to check anything in the "Build environment"-tab. But for most projects we set xterm as the colour map to colourcode the build output for better readability.
* In the "Build"-tab you can specify a variety of different build steps. We usually only select "Execute shell". In the console that appears you can define environment variables and execute your ci-build.sh file. We try to execute as little code as possible here and instead add all necessary code in the ci-build file.
* In the "Post build actions"-tab you do not need to check anything for the basic implementation. Depending on the project it might make sense to send some notifications, publish reports or trigger some Jira actions.

### Github
* Add ghprb webhook in GitHub configuration. (You will need admin rights for the repository to do this)
  * Go to the settings tab on the repository and navigate to "Webhooks"
  * Add a new webhook, specify `https://ci.liefery.com/ghprbhook/` as the payload url, set the content type to `application/x-www-form-urlencoded` and enable SSL-verification
  * Check "Issue comments" and "Pull requests" when selecting individual events
* As a last step you need to give Jenkins writing access to the repository otherwise the indication whether a build failed or not will not show up in the pull requests.

For people who prefer to watch a talk about the topic, here is the [recording](https://zoom.us/recording/detail?meeting_id=ubbcaRJ9TwK56XUuQEWvMA%3D%3D). The video is ~17 minutes long.

### Terminating a running CI job

**You shall not attempt this.**
Once a job has been scheduled, there's no graceful way to stop it. If you try and click the stop button next to the job in the list, there's a high probability the database connection won't be released. All further jobs that get assigned to this worker are going to fail. See the "Restarting Jenkins" section to restore the weave of fate, or persist in the doomed world you have created.

### Debugging CI problems

Should you need a direct access to the CI servers, they are available over SSH using the following commands:
```sh
ssh jenkins@ci.liefery.com # primary node
ssh jenkins@ci-worker01-dev.liefery.makandra.de # secondary node, or build_node_1
```

If these commands fail with “Permission denied“, please, first ensure your SSH agent has loaded your keys, by running `ssh-add`. If that doesn't help or if it's your first time connecting to these servers, ask in the #makandra channel to make sure your key is authorized to log in.

You might want to access the servers to inspect or download a build artifact (see also, [scp](https://man7.org/linux/man-pages/man1/scp.1.html) for downloading files over SSH), to retry a failing command, or to clean up a broken workspace (for example, a dirty `node_modules` folder).

### Restarting Jenkins

To restart the Jenkins app go to https://ci.liefery.com/manage and click the “Prepare for Shutdown” button at the very bottom of the page. This will prevent further CI jobs from being started. Wait until the running jobs are completed. Once all is clear, visit https://ci.liefery.com/restart and click “Yes” to initiate the restart.

Most probable use case for this is when a database connection wasn't released properly, and now one of the workers is always failing.
