# `test-deploy`

Test some deploy ideas with Github Actions + Environments
- https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment
- https://docs.github.com/en/actions/learn-github-actions/contexts
- https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets
- https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/configuring-tag-protection-rules

## What we want

- CI checks run on all pull requests
- CI checks run on main too, and if they pass, the Dev environment is automatically deployed
- Staging is deployed from main, when commit is tagged
  This is a bit of problematic to express in Github Actions.
  Environment protections need to be applied too.
- Production is deployed from the same commit as staging, after a manual approval,
  Environment protections need to be applied too.


## Check Github Actions to see the behaviour:

https://github.com/LokaHQ/github-environments-deployment-demo/actions

## Testing procedure

We can follow the behaviour of each commit, and the state of the workflow file, on the github actions page.
We can't follow the behaviour of the changes to the "Environments" in Github settings,
so make sure to document those in each commit message.

## Scenarios to test

- push/merge to main
- push a tag from top of main
- push a tag from the history of main
- push a tag that's not on the main branch


## Issues:

- [cancel pending](https://github.com/orgs/community/discussions/5435)


## Repository settings:

- default branch: main, protected
- allow merge commits: disabled
- allow squash merging: enabled, Default to pull request title and description
- automatically delete head branches
- projects: disabled

Github Environments have restrictions:
- Staging: 1 rule, only allow tags matching `v*`
- Production: 3 rules, tags matching `v*`, manual approval, wait timer 15 minutes

Tag ruleset configured:
- Active
- Target tags: `v*`
- Tag protections:
  - Restrict creations: disabled
  - Restrict updates: disabled
  - Restrict deletions: enabled
  - Require deployments to succeed: Dev
  - Require signed commits: disabled
  - Require status checks to pass: disabled
  - Block force pushes: enabled

Branch protection (on main):
- Require a pull request before merging
- Require status checks to pass before merging
  - Require branches to be up to date before merging
  - Status checks that are required: `ci`
- (the rest are disabled, TBD)
