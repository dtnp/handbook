<!-- TOC -->
## Contents
[](#top)
- [Example Workflow](#example-workflow)
  - [CI](#ci)
  - [Staging](#staging)
  - [Prod](#prod)
- [Pantheon Orb](#Pantheon-orb)
- [Staging Branch](#staging-branch)
- [Dev / Preview parallel deploys](#dev--preview-parallel-deploys)
- [Further Improvements](#further-improvements)

## Overview

Our CI standards should be changed to adopt the following three ideas
1. Make use of the Pantheon orb
1. Add a staging branch and workflow to allow for easy hotfixes between release trains
1. Allow for deploying to dev and preview in parallel

## Example Workflow

The general layout of the workflows should be as follows

### CI
* preflight
* build artifacts and run tests
  * all artifacts should be persisted to the dist directory for inclusion in the release
  * artifacts include swagger docs and the app itself
  * tests can include unit, component, and integration tests
* Plan (or validate) terraform
* Manual approval for deploys
* Publish a github release and docker containers for the deploy
* Deploy to dev and preview if approved

### Staging
* Build artifacts and run tests
* Check for unreleased versions of dependencies
* (Future) Deploy to pre prod

### Prod
* Build artifacts
* Should we run tests in the prod pipeline?
* Plan tf
* Manual approval of tf
* Deploy
* Clean up candidate releases

## Pantheon Orb

The [Pantheon Orb](#) should be used anytime jobs are duplicated between repos.

## Staging Branch

Each repo should have a staging branch which all pull requests will be merged to. On release train day, staging will be merged to master/main and the deploy can occur as usual.
This allows for hotfixes to be applied directly to master/main without releasing features.

## Dev / Preview parallel deploys

Dev and preview should be treated in parallel environments for the following reasons
* Allows for parallel development
* Allows for db migrations to be non-blocking to other team members
* Allows for faster deploys to both when needed

## Further Improvements

In addition to the changes demonstrated in the activator the following should be added to the Pantheon orb and our workflows
1. Remove plan-tf and replace with validate-tf
   * This should not require an environment so can be run once before the workflow splits between dev and preview
1. Add a check to see if the Pantheon orb can be updated
   * This will help keep the orb up to date in our repos
1. Convert build app to be an orb job with a swagger boolean
   * Build app has not been added to the Pantheon orb yet
   * It should have a boolean for whether the repo has swagger docs