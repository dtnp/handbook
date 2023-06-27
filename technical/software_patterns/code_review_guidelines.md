# Code Review Guidelines
**Topic:** Recommendations for performing code review and having your code reviewed.

**Maintainer:** Standards Guild (@mhijazi-bf, @chronoslynx, @JHarperFox, @bf-dbubel)

**Version:** 1.0

<!-- TOC -->
## Contents
[](#top)
- [Overview](#overview)
- [Goals](#goals)
- [Review Process](#review-process)
  - [Choosing reviewers](#choosing-reviewers)
  - [Design review](#design-review)
    - [Making suggestions](#making-suggestions)
    - [Handling suggestions](#handling-suggestions)
  - [Definition of Done](#definition-of-done)
  - [Final review](#final-review)
- [Guidelines](#guidelines)
  - [As an author](#as-an-author)
  - [As a reviewer](#as-a-reviewer)
<!-- /TOC -->

## Overview

All pull-requests to engineering repositories must be reviewed (barring emergencies where the 
risk is accepted by the author or responders) before merge. This helps us ensure that changes
are effective, maintainable, performant, and secure.

## Goals

Our systems should be

- **Correct**: code should do what it sets out to do. This should be validated as best as possible using automated tests.
- **Understandable**: we should understand where and why code fails when it does. This not only includes metrics and logging but also readability: unreadable code is hardly understandable!
- **Simple**: avoid unnecessary complexity. If it isn't necessary [you (probably) aren't going to need it][yagni]
- **Evolvable**: code can be changed and expanded upon as requirements change without undue stress
- **Performant**: appropriate data structures and algorithms should be used, but premature _micro_ optimizations should be avoided.


## Review Process

Code reviews happen in two stages: an initial design review/draft stage, during which the
PR is iterated upon and tested in our development environments where appropriate; and a final review stage which focuses on the correctness and maintainability of the proposed changes but _not_ their design.


### Choosing reviewers

Each repository should have as code reviewers the engineers most knowledgeable about the system, and they are required as a reviewer for all PRs to that repository.

If you are issuing a change to a system owned by your team, prefer reviewers on your team to those on other teams _when appropriate_. This keeps our teams in sync and lowers the amount of context switching our engineers experience.

When appropriate, its recommended that you find a domain expert for the topic at hand to review your PR on top of this. For example: if you're optimizing a PostgresQL query then it's recommended that you reach out to one of our database-minded engineers who can help you validate the performance of your changes.

### Design review

Draft PRs should be opened for changes as soon as is reasonable: this stage is where the bulk of review should occurr. The earlier your PR is open and you can receive design feedback the better as it shortens iteration cycles. Your PR should include all relevant context for your implementation. Why was this design chosen? What are the caveats and risks the team has accepted? Are there any "gotchas" or sharp edges to be aware of?

When opening a PR be mindful of its size: when making large changes you are encouraged to break your PRs up into a stack of atomic changesets to ease the review burden.

This is the time to, as a reviewer, discuss the basic design of the proposal. 

#### Making suggestions

As a reviewer your job is to ensure that any work meets our [definition of done](#definition-of-done). If you have suggestions on how something can be improved, please
add it to the PR as a comment _including_ relevant details as to _why_ your suggestion is 
both relevant and improves the work as a whole.

Once the PR author has responded to your comments to your satisfaction, please mark the comment thread as resolved. If it was resolved by a discussion outside of the PR itself you **must** document the resolution unless it was an obvious change of fix.


#### Handling suggestions

Suggestions from reviewers may be accepted and rolled into your changeset, deferred for future work, or declined. If declining or deferring a suggestion or proposal, please respond to your reviewer with details on why your are declining it (is it out of scope? a nitpick? better to do later?).

If you address a comment or suggestion on your PR, please respond to the reviewer who suggested it.

#### Definition of Done

Over time each team may evolve their own definitions of done, but the following should be used as a basis for them.

A change can be considered Done when:

1. All acceptance criteria are satisfied
2. All acceptance criteria are validated by automated or manual tests that are documented in the repo itself
3. The design meets our [goals](#goals)
4. The change satisfies any non-functional requirements the team has
5. The stakeholder approves of the change
6. The change has been tested in one of our development environments

### Final review

Once a change has been opened for final review, reviewers should focus on the performance and correctness of the change but _not_ the design of the change itself. Where possible the change should be deployed to and validated in a production-like environment to lower the risk of an actual deploy once approved.

If a critical defect is identified, a fatal design flaw discovered, or other definition-of-done violating issue the change should be sent back and the flaws addressed.

This stage should be relatively short as the bulk of the review should happen prior to this stage.

## Guidelines
### As an author

Be mindful of how much you're asking your reviewers to review. They're also handling their own issues and tickets, so break your changes up into digestible chunks and include any relevant context so they don't need to hop through tickets, Teams discussions, and email threads to understand what they're reviewing.

### As a reviewer
[Watch this Video](code-review-best-practices). It's excellent.

When you see something you like or excited by, call it out! [Code review is too often antagonistic](code-review-toxicity), and we're all in this together. Point out and comment on the positive aspects of a change!

When you do disagree with a change remember that PRs should be collaborative not antagonistic: your comments should be positive and educational and not deride the choices of the author. If you don't like or agree with something then explain why and provide alternatives with justification. Give the author the benefit of the doubt!

When choosing PRs to review please review PRs in need of urgent review (say, to resolve active incidents), then PRs from your team members, then PRs for systems you are a code owner of, then PRs for other teams.

[ [TOP](#top) ]

[yagni]: https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it
[code-review-best-practices]: https://www.youtube.com/watch?v=a9_0UUUNt-Y
[code-review-toxicity]: https://medium.com/@sandya.sankarram/unlearning-toxic-behaviors-in-a-code-review-culture-b7c295452a3c
