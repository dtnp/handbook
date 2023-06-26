# CI/CD

Instructions for setting up a new repo can be found [here][ci-setup]

### Guardian CI

[Guardian CI][guardian-ci] is our [Circle CI Orb][circle-orb]. It is open source and contains shared jobs that we use in many repos. The goal is to have consistent and DRY CI configuration.

#### Linting

Guardian CI has two linting options [golangci][golangci], which is preferred, and goimports.
These are often run manually as part of the normal development and testing process, as well as automed in CI/CD.

#### Releasing a new version of the orb

1. Create a branch, make changes, push them.
2. Publish dev version of the CI orb by ...
3. Test dev version in a repo using the ... tag
4. Merge to main with a commit message that begins with `[semver: <patch,minor,major>]`
5. Publish the new version
6. Our CI configs use the latest from major version `1` so unless bumping a major version, repos are automatically updated.

[circle-orb]: https://circleci.com/orbs/
[golangci]: https://golangci-lint.run/
