So you want to update Node and Yarn
===

To update Node and Yarn we some help from our friends at Makandra to update the versions on the servers.

It is important to only update servers when the code that bumps the version is present on the server. For example, you should update the staging servers when your code is on staging, but updating the versions on production should be postponed until the code is ready to be deployed.

## Yarn
In the Slack workspace Liefery-Makandra specify the version of Yarn you would like, and ask them to update all servers.

## Node
The Node update is more involved. While Yarn can be updated at the server level, Node must be updated at the user level. For example, here is the current list of Users that require a Node update for the staging deploy to run. Makandra needs to update the Node version for all of them.

| Users | Server |
| --- | --- |
| deploy-feibra-backend_s | app01-stage.liefery.makandra.de |
| deploy-feibra-backend_s | app02-stage.liefery.makandra.de  |
| deploy-liefery-backend_a | app01-stage.liefery.makandra.de |
| deploy-liefery-backend_a | app02-stage.liefery.makandra.de  |
| deploy-feibra-backend_s | saas101-stage.liefery.makandra.de |
| deploy-feibra-backend_s  | saas102-stage.liefery.makandra.de |
