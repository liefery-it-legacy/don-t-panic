# How to update Ruby version across our applications

We use Ruby in multiple projects and to a various degree. We want to use
the same version across all these projects.

For **backend** and **whitelabel** we use what Makandra calls "unmanaged Ruby"
on our servers. This means they don't manage it for us, instead, we manage
it ourselves using their [opscomplete gem](https://github.com/makandra/capistrano-opscomplete).
When we deploy, the gem looks to make sure the correct Ruby version is
installed, and if it's not, installs it, and then restarts passenger.
The gem uses rbenv, so each application using it needs a `.ruby-version` file.

**Homepage** and **Loggery** use Ruby for deployment which means that the new version only has to be installed on Jenkins.

**Mergebot** and **GitHub JIRA bot** only run on Jenkins which means the the new
version has to be installed on Jenkins.

**getupbot** and **oepag csv converter** are deployed with Heroku. The csv converter's
deployments are automatic, the getupbot's are not.

### Workflow

1. Run `rbenv install <YOUR RUBY VERSION>` on both Jenkins nodes.
2. Submit PRs that bump the version for Backend and Whitelabel Tracking.
3. Make sure that all builds are green.
4. Merge the PRs.
5. Make sure the staging deploys are successful.
6. Production deployments can happen during our regular deployment cycle.
7. Update the Ruby version on all other applications. Make sure the staging deploys are successful.
8. Deploy the other applications if needed.
9. Handle the customer web app (see below).

## Customer Web App

**Customer Web App** uses Ruby for deployment and for asset compilation but does not
require a passenger restart. This application is also "unmanaged Ruby", but we do not use
the opscomplete gem because our capistrano version is out of date and not compatible. We
therefore have to install Ruby ourselves. The process is as follows:

1. Log onto both customer web app staging servers with `geordi shell staging -s` (with this command you can
choose which server to log onto).
2. Install ruby with `rbenv install <YOUR RUBY VERSION>`.
3. If you get a message from rbenv telling you that this version is not installed, follow the
rbenv instructions to install it. It should look something like this:
`cd /home/deploy-liefery-customer-web_s/.rbenv/plugins/ruby-build && git pull && cd -`
4. Install ruby with `rbenv install <YOUR RUBY VERSION>`.
5. Make sure you're using the right version and the correct version of bundler with:
`rbenv shell <YOUR RUBY VERSION> && gem install bundler && rbenv global <YOUR RUBY VERSION>`
6. Make a PR to bump the Ruby version.
7. Merge it. Watch the [staging deploy on Jenkins and confirm that it worked.](https://ci.liefery.com/job/customer-web-app-staging-deploy/)
8. Log onto the customer web app production servers and install the correct Ruby version following
the same steps as above.
9. Deploy to production.
10. Celebrate with cake.
