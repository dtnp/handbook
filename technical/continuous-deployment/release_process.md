# Contents 
[](#top)
<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->

- [Release Process](#release-process)
    - [Contents](#contents)
    - [Overview](#overview)
    - [What may be released continuously](#what-may-be-released-continuously)
        - [Handling database migrations](#handling-database-migrations)
    - [What may be released continuously behind a feature flag](#what-may-be-released-continuously-behind-a-feature-flag)
    - [Documenting releases](#documenting-releases)

<!-- markdown-toc end -->
## Overview

This document describes how we as an engineering team can release changes into our production environments. It does _not_ discuss pre-production environments

[ [TOP](#top) ]

## What may be released continuously

Changes may be released once merged to a repository's trunk if its release would not break existing user (or operator) workflows.

Example changes that meet this criteria include:
- additive changes, such as exposing an additional field from an existing API (larger additive UI changes should still be feature flagged)
- backwards-compatible database migrations
- new APIs that did not previously exist
- bug fixes and performance improvements that do not change the semantics of an existing feature

### Handling database migrations

Database migrations should be backwards-compatible by default, and should be released in two stages:

1. The first migration introduces the new table/column/functionality but does not eliminate the existing functionality
2. All code using the new functionality is released and tested
3. Once all new functionality has been validated, another migration can be applied to drop the now-deprecated functionality

[ [TOP](#top) ]

## What may be released continuously behind a feature flag

Some changes that may not be released continuously on their own may be released continuously if properly feature flagged. This includes:

- backwards-incompatible changes to existing functionality
- new functionality that will be _immediately_ evident to our users, such as new UI pages or designs
- changes that will have an impact on our data sets that must be communicated to our customers

[ [TOP](#top) ]

## Documenting releases

New releases should still be aggregated into the current sprint's Release Train ticket.
This will allow us to audit what is being released and update our release notes appropriately
