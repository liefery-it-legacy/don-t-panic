# Team Raccoon

This document describes processes that apply to the members of Team Raccoon.

## Dependency updates rotation

Every second week (starting from the team meeting on Wednesday) two new people
become responsible for updating Ruby, JavaScript, and Dart dependencies.
The current schedule is available [here](https://github.com/liefery/don-t-panic/wiki/Team-Raccoon#dependency-updates-rotation). We take one
Backend Developer and one Frontend / Mobile Developer in order to encourage
pairing and knowledge transfer.

The responsibilities are:
1. Make sure that [Depfu PRs related to Ruby and JavaScript dependencies](https://github.com/pulls?q=is%3Aopen+is%3Apr+archived%3Afalse+user%3Aliefery+label%3Adepfu+NOT+%5Belixir%5D+)
are handled. Most of the PRs will be quickly reviewed and merged by other
people. However, if there's more work involved (because of failing tests)
a PR may stay open for a longer time. The task is to triage these long-open
PRs. Please timebox fixing a PR to 30 - 60 minutes. If it's not enough time,
close the PR and create a follow up ticket in
[Dependency updates epic](https://liefery.atlassian.net/browse/LIEF-14224).
2. Make at least one improvement related to our dependencies. This may be:
   - updating a Dart dependency
   - working on a ticket from [Dependency updates epic](https://liefery.atlassian.net/browse/LIEF-14224)
   - reviewing `Gemfile` for dependencies with locked versions and checking if
     we still can't update them
   - looking at Depfu dashboard for dependency conflicts and checking if we can
     resolve them
   - looking at Depfu dashboard for PRs that we closed and following up on them
   - configuring Depfu for a repository where it's not configured

During the team meeting we look at the improvements made in the last two weeks
and share the knowledge about them.

## Mobile deployments rotation

Every week during the retrospective we pick one person responsible for the
deployments of the mobile app. This includes:
- keeping the "On Staging" column clear by making sure that QA happens
regularly and that tickets that require a developer for QA are QAed
- being the fallback QA person
- freezing deployments to staging by disabling
https://ci.liefery.com/job/fluttery-build-internal/ if we can't keep up with
the QA workload
- deploying to production on Monday and Wednesday

The schedule of the rotation is available in the #mobile-deployments channel.
