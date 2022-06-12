Developer notes
===============

This document is for people who maintain and contribute to this
repository.

Commit messages
---------------

Commit messages follow the [Conventional
Commits](https://www.conventionalcommits.org/) format that is also
used by Tutor and Open edX. See
[OEP-0051](https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0051-bp-conventional-commits.html)
for details.

We enforce the prescribed [type
prefixes](https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0051-bp-conventional-commits.html#type)
to be used in commit messages via
[Gitlint](https://jorisroovers.com/gitlint/).

Development
-----------

- To test, launch the Github Actions Workflow in your forked repository ```Actions -> Test this action```.

Submitting PRs
--------------

- Clone this repository to your local development environment
- Install pre-commit on your local development environment and run `pre-commit install` in the root of this repo
- Commit your changes to one of the following branches: `alpha`, `beta`, or to `next` if you are a core committer
- Note that the Github repository automatically runs QC tests on these branches: alpha, beta, next, main
