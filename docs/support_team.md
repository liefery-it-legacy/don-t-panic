# Support

Every Monday afternoon we pick a support team for the next sprint.
The support team has 2 people of which **at least one has production access** and **is working every week day**.
If necessary, a third fallback person can be nominated to fill in the gaps for Fridays or e.g. for Monday/Tuesday
if one of the 2 goes on vacation the next week.

When picking a support team we should try to include and onboard new team members.
They can take the "observer" role once for one week before starting support themselves.
This does not mean they are responsible for support during this week but they can already read all
the topics coming up in the support channel and pair with the the others on finding a solution.

The new support team starts their work at the beginning of the iteration — Wednesday morning.

The support team's responsibility is to monitor and respond to the following 2 slack channels:

* MS Teams Liefery #backend: in this channel OPS colleagues can post urgent support requests and questions.
  All requests in this channel are urgent and are typically blocking time critical processes.
  Therefore we need to take care of these requests immediately and aim to give a first response within 5 minutes.
  A first response can be a question to help narrow down the issue, a suggested solution or workaround or at the very
  least a quick reply that we are looking into the issue.
  In case you are on the support team and don't know the answer, please ask your colleagues for help.
  If you are not on the support team and know the answer, don't just answer it there, but rather help the support 
  team answer the request themselves.
  You can find this channel simply by going to the search bar in your MS Teams app and searching for #backend.

* #bugsnag-backend: in this channel we receive notifications about exceptions and other alerts generated from our system. These should also be looked into quickly in order to determine the potential production impact. A quick response should be given within the slack channel to the rest of the team that the issue is being handled.

The support team should make sure to monitor these 2 channels during normal work hours. The highest chance of support requests coming in is currently between 16:00-18:00. To be able to provide a 5 minutes first response time, please enable slack notifications for this channel when you're on the support team.

When a Bugsnag error or support request arrives and a first response has been provided, investigate the impact of the given problem or question. If it doesn't require an immediate workaround create a Jira ticket to be handled by Edge. In these cases also leave finding the cause and coming up with a solution to the person working on the ticket.

When answering support requests please take into account that the solution the requester is asking for might not always be the best one. Often it is necessary to ask questions and understand about the underlying problem and suggest a better solution. We can use this Wiki page to collect common problems and solutions: [https://github.com/liefery/liefery-backend/blob/master/docs/emergency_handbook.md](https://github.com/liefery/liefery-backend/blob/master/docs/emergency_handbook.md)

The support team is also responsible for doing the [deployments](https://github.com/liefery/don-t-panic/blob/master/docs/deployments.md) in the given week.

We have a Slack reminder on Monday at 17:00 to pick a new support team. The
current support team is responsible for making sure that a new support team is
indeed picked.

## Handling support requests

We have a list of [common support requests](https://github.com/liefery/don-t-panic/wiki/Typical-Support-Requests) which should be updated if a request comes in recurringly.

What makes a proper support request? You might be tempted to help everyone, and if you try to do it all by yourself,
before long you're going to find yourself spending most of the day in the support channel. Here's a set of guidelines
to help you handle the requests in a more efficient manner and avoid getting overwhelmed.

* Does it look like a bug in the system?

Verify it and create a bug ticket, otherwise explain why the system is not behaving as expected.

* Does it throw an exception or show an error page?

Tell what's causing the error.

* Is it a question about how the system works that has a quick answer?

Try to answer it.

* Is it a general lack of knowledge about the system that comes from a lack of training?

Forward to Dana or Josi to possibly arrange a training session.

* Is it a question that has an answer in the documentation (for example, API docs)?

Give a link to the relevant section of the docs.

* Is it related to what business people do, but doesn’t have anything to do with the systems that we maintain?

This is a kind of request that should be addressed to someone else.

* Is it a question about getting some data from the system which can be extracted by the users themselves?

Let them do it.

* Is it a request to perform an operation in the system that can be done via the admin panel?

Point to the relevant admin page.

* Is it a request to override some data in the database manually?

Estimate the difficulty and risk. Err on the side of refusing the request. Think of ways to restart/recreate the thing
instead of changing the existing thing.
