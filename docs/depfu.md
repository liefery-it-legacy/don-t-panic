# Depfu
We use [Depfu](https://depfu.com/) to update our dependencies. Depfu will open PRs
in our apps each day with package updates. It is our responsibility to look over the PRs
and merge them by adding the "merge when CI passes" badge.

## Where we use Depfu
We use Depfu for our Rails and Phoenix applications. It also updates
our javascript dependencies within those apps.

Depfu for Phoenix is currently still in beta, so it's possible that strange
things will happen. The Depfu people are very nice and are aware that we
are beta testing for them. Please report any strange issues to [Team Hedgehog](https://github.com/liefery/don-t-panic/wiki/Teams#team-hedgehog-backend)
who will then troubleshoot and if necessary, contact the Depfu team.

## How to handle Depfu PRs
We shouldn't just yolo merge a Depfu PR. Please take the following into consideration
before merging.

* make sure tests are passing
* read the changelog about changes between the previous and the new version
  * was an interesting new feature added that we might want to use? -> create a ticket
  * was a breaking change introduced? -> test it yourself and potentially add commits to the PR
  * do any of the changes touch functionality of the gem we use? -> add a QA comment to test this functionality
* is it a major version bump? -> test it thoroughly yourself
* add `QA: none` or `QA: <whatever should be tested>` as a comment to let the merge bot create a Jira ticket for the Depfu PR
* add the label `merge when CI passes` if everything is fine, so that the merge bot can correctly merge and track this ticket.


## How to add Depfu to a new project
This needs to be done either by someone who has admin rights, or together with someone
who has [admin rights](https://github.com/liefery/don-t-panic/blob/master/Readme.md#people-with-admin-rights).
To add Depfu to a repo on Github, log in to Depfu using Github. From there
you should be able to add or remove repos, as well as make a number of configurations.

Once Depfu has been added, it will start opening PRs. CI won't automatically run for them
though, because Jenkins will not run PRs for an external untrusted source. To
enable this, an admin needs to comment on the PR with the
[magic words](https://wiki.jenkins.io/display/JENKINS/GitHub+pull+request+builder+plugin): "add to whitelist".
This only needs to be done on one PR. This should trigger Jenkins for the other PRs as well,
and you will be able to continue normally.

## How to disable Depfu for a project

Similarly to adding Depfu to a project, this requires [a person
with admin rights](https://github.com/liefery/don-t-panic/blob/master/Readme.md#people-with-admin-rights).
Please, follow the [same procedure](https://github.com/liefery/don-t-panic/blob/master/docs/depfu.md#how-do-add-depfu-to-a-new-project) but remove the repo instead. Meanwhile, the creation of PRs can also be stopped by a non-admin — log in to Depfu,
go to the project settings and choose "Paused" from the "Update Strategy" dropdown.
