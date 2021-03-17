# Tickety-Tick

Tickety-Tick is a browser extension that automatically generates commit
messages and branch names from JIRA tickets. Thanks to these naming conventions,
tickets in JIRA have branches, commits, and pull requests associated with
them, and tickets move automatically between columns in the sprint board.

1. Download [Tickety-Tick](https://github.com/bitcrowd/tickety-tick)
2. Find [extension settings](https://github.com/bitcrowd/tickety-tick#advanced-configuration) in your browser
3. Turn off auto-formatting of commit messages
4. Set "Commit Message Format" to `[{id}] {title}`
5. Set "Branch Name Format" to
   `{type}/{id | slugify | uppercase}-{title | slugify}`
