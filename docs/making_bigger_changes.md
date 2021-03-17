# Architectural Changes / Changing the way we do things

We strive to continuously improve our code base, this means that we don't want to do things just because "that's how we used to do it". We encourage heathy discussion and everyone's input is welcome.

Before changing all the things it is best to check with colleagues what they think. Especially for **bigger changes** it is good to get the input of multiple people to make sure we're doing the best change and everyone is on board knowing the _why_ of these changes.
We should also try to avoid having greater arching architectural discussions in a PR which implements a feature/bug fix. This should only be the case if it is immediately relevant or for some reason hard to reverse. Bigger architectural discussions tend to side track the PR resulting in delays, blockers and big discussions that aren't well traceable. In such a case, it is ok to announce that you will stick with the way we do things for the PR and kick off the change process as described below separately from the PR.

Therefore it's recommended to:

1. proposal - Explain what, how and why in order to facilitate discussion. Proposals usually are text documents that everyone can comment on, they may also be PRs.
2. meeting (optional) - If the proposed change is not accepted in the proposal stage, a meeting can further help to facilitate the discussion and find a solution that everyone agrees on.
3. documentation - The decisions taken from the previous steps should be documented.

Examples of such documentation include [service object guidelines](https://github.com/liefery/liefery-backend/blob/master/docs/service_objects.md) and [acceptance tests moving from cucumber to rspec and page objects](https://github.com/liefery/liefery-backend/blob/master/docs/automated_testing.md#backend-and-acceptance-testing).

Note that our code base is big and often changing all things at once is unrealistic, so we generally adopt a strategy of writing new things with the new guidelines and adopting old code as we touch it. Leave the code cleaner than you found it.

Even if all changes could be done in one PR it is often better to split it up in smaller PRs for better reviewability and earlier feedback.

For **smaller changes** it can often be enough just to open a PR to discuss the changes. For instance, you want to change a linter rule: Open a PR with the change and see what other people think.