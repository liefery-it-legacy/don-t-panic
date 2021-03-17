### Deployments

Alpha and staging deployments are automatic and triggered once something is
merged to master.

Production deployments should happen a few times per week, but maximum once per day. See [QA for how it does affect the deployment](https://github.com/liefery/don-t-panic#quality-assurance).
Specific deployment information for the applications can be found in the
specific repositories either in the README or in separate documentation.

The support team of the iteration does the production deployments. Every day in
the morning, the support team goes to the #deployments channel in Slack and
searches for the most recent deployment proposal. If it wasn’t deployed, they
click the "Deploy" button and monitor the deployment. There may be multiple
deployment proposals in the #deployments channel. The most recent one should
contain the highest number of QAed changes and therefore this one should be
deployed, in order to avoid deploying things in two deployments unnecessarily.

If there are no deployment proposals for two consecutive days, the support team
should search for the reason in coordination with people doing QA (too high QA
workload, rejected tickets) and try to unblock the next deployment. 

The tool that we use for orchestrating the deployments is [Deployer](https://github.com/liefery/deployer).
The README of the project contains more technical information about how the
deployment workflow functions.
