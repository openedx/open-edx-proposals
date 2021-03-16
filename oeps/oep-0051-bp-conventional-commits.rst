==============================
OEP-0051: Conventional Commits
==============================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0051 </oeps/oep-0051-bp-conventional-commits>`
   * - Title
     - Conventional Commits
   * - Last Modified
     - 2021-03-16
   * - Authors
     - Ned Batchelder
   * - Arbiter
     - Feanil Patel
   * - Status
     - Accepted
   * - Type
     - Best Practice
   * - Created
     - 2021-02-01
   * - Review Period
     - Until 2021-03-15
   * - Resolution
     - TK
   * - References
     - `Conventional Commits`_
     - `Change Transparency`_


Abstract
========

Commits should be clearly labeled for their effect on consumers of the repository.  To do this, we adopt the `Conventional Commits`_ guidelines, which detail structured commit messages that clarify the impact of each commit.

This is part of our `Change Transparency`_ initiative.

Motivation
==========

Developers should describe the changes they make.  This information is valuable to many audiences: deployers, integrators, end-users, operators, and so on. Developers are in the best position to accurately describe their work and its implications.

The nearer to the change that information is captured, the easier it will be to write, and the more accurate it will be.  Changes made by developers are consumed by many people, from the pull request reviewer to the final user of the product.  The more the developer can explain the change, the more easily the change will be understood and used, and the more efficient the overall development process will be.

Commit messages are the closest text to the change itself.  The more information-rich we can make commit messages, the more useful they will be.


Specification
=============

We are adopting the `Conventional Commits`_ spec, with our own specifics.

.. note::
   Many people are familiar with the `Angular commit message format`_, which uses conventional commits and inspired the Conventional Commits spec.  We are **not** adopting the Angular rules.

Git commit messages have these parts::

    <type>: <subject>

    <body>

    <footer>

Type
----

We use these commit types labels:

* **build**: anything to do with building or releasing the repo: Makefile, tox.ini, Travis, Jenkins, GitHub actions, and so on.

* **chore**: a repetitive mechanical task such as updating requirements or translations. See `Discussion`_ below for how "chore" can hide important information.

* **docs**: documentation-only changes. Documentation changes for other types of work should be included in those commits. This includes more than the formal docs for a repo, it also covers any change that updates words describing the work, for example READMEs, ADRs, docstrings, annotations, comments, and so on.

* **feat**: a new feature, or a change to an existing feature. Anything that changes the set of features.  Public API entry points and their behavior are part of the feature set of a product, so changes to those should be marked with "feat".

* **fix**: bug fixes, including changes to the behavior of features, and changes that remove or mitigate security vulnerabilities.

* **perf**: performance improvement.

* **refactor**: code reorganization that doesn't change behavior from the repo consumer perspective.

* **revert**: undo a previous change. The previous commit message, including the type label, is included.

* **style**: improve the styling of the code.

* **test**: test-only changes. Adds missing tests or corrects existing tests. Tests accompanying other types of changes go into those commits.

* **temp**: temporary experimental or exploratory changes that are not meant to be permanent.  Occasionally changes have to be pushed to GitHub or even production that are intended to be short-lived. For example, debugging continuous integration, or ad-hoc debugging on live systems.


Breaking changes include an exclamation point as part of the type label::

    feat!: remove the ability to author courses

Commits should be separated to make it easier to review them before merging and easier to understand them later.  Some types work well in a combined commit, such as a feature along with its tests and documentation.   Refactor, style, chore, and temp commits should be separate from more significant work.

If a commit mixes types, use the most important type label in the commit message.  The priority order for the types is generally: revert, feat, fix, perf, docs, test, build, refactor, style, chore, temp.

Scope
-----

The `Conventional Commits`_ spec includes an optional parenthesized scope after the type label.  Open edX repos are large and varied, making standardization of scopes difficult.  We won't use scopes for now.  This could change in the future.

Subject
-------

Commit message subjects should be short enough to fit on one line.  We aren't putting a hard limit on character length, but 70 characters is a good time to turn your attention to the body of the commit message.  Put more information in the body of the commit message to fully explain your change.

Don't include Jira or GitHub issue numbers in the subject.  The body is the right place for links to supporting information.  The subject is precious real estate that should be used for words.  While it is true that it takes more work to get information from the body than from the subject, we are emphasizing writing longer commit messages, and so need to be good at reading the body anyway.

Body
----

The subject of the commit is rarely enough information to fully understand the commit.  The body can contain as much information as you like.  Be generous.  Take a moment to think about what you would want to know if someone else had authored this commit.

The more information you can put in the body of the commit message, the better. It's not unreasonable to have two paragraphs of explanation in the body.  Especially important are the reasons for the change, or other factors that won't be apparent from the code itself.

Breaking changes especially should have detailed information about the implications and alternatives, including a ``BREAKING CHANGE`` footer.

Include references or links to supporting information, such as Jira or GitHub issues.  Some Jira issues will be private to edX.  It is better to link to a private issue than no issue at all. The issue could be made public, and at least its existence is made clear, so people can ask for the information.

Larger decisions should be recorded in Architectural Decision Records, as explained in `OEP-0019`__.

__ https://open-edx-proposals.readthedocs.io/en/latest/oep-0019-bp-developer-documentation.html#adrs

Footer
------

Breaking changes must have a ``BREAKING CHANGE:`` footer in the body.  No other footers are specified.



Discussion
==========

Conventional Commits asks us to categorize changes into a small number of categories.  There will naturally be cases where it isn't obvious which commit type to use.

Choosing the commit type label to use for a commit is important, but it's only the first step.  If you have doubts about which commit type to use, choose the highest-priority type that could apply.  Then write a detailed body explaining the full complexity of your change.

**feat vs fix**: some user-visible changes to features could be classified as "feat" or "fix".  Choose "feat" if the change adds to the set of features.  Choose "fix" if the change affects how a feature behaves.  Yes, this is still subjective.  

**Breaking changes to features**: changing how a feature works is not a breaking change.  For example, users are sent to a new experience instead of the old experience. This is not a breaking change.  It should get a "feat" label, but not a "feat!" label.

**Deprecations**: deprecations happen in two steps: the announcement of the deprecation, and the eventual removal.  The first step is important for people to recognize, but is not a breaking change.  Use clear strong words in the commit subject to be sure people understand the importance.  The second step is usually a breaking change, if a component has been removed.

**Pinned dependencies**: updating the version of a pinned dependency is "just" a chore, but can bring significant changes to the depending repo.  Conventional commits can't solve this problem.  Reading the commits for a repo won't show that a "chore" might have the effect of a "feat".

**Merge commits**: Commits that git generates (such as merge commits) do not follow these guidelines.  This is not a reason to avoid those commits, though you may want to for other reasons.  The structure of conventional commits will still be useful if merge and other auto-generated commits are in the commit history.

If you are interested to see other discussion about these sorts of questions, the `Conventional Commits repo issues`__ have a number of threads.

__ https://github.com/conventional-commits/conventionalcommits.org/issues


Rationale
=========

The existing Conventional Commits standard is a familiar and widely adopted standard.  It's a good place to start our own guidelines.  It builds on our recently adopted pull request template to help focus developers on providing helpful information about their changes.

We've adapted the guidelines to our own projects, and started small.

Here's an example of a conventional commit, with a one-line subject, and details in the body::

    build: handle private.txt files properly

    The requirements/edx/private.txt file is for dev's own private package
    needs.  There are two installation mechanisms in edx-platform, and
    neither handled the file properly:

    - `paver install_prereqs` had the wrong file name.  The file was moved
      almost three years ago, and paver wasn't kept up.

    - `make requirements` used `private.*` which included private.in, which
      pip-sync balks at.

    Fixes: BOM-2345


Open Issues
===========

Some discussions concerning conventional commits are not yet resolved.

Scope
-----

Is there a standard for scopes that could be useful?  Component names could be inferred from the files changed in the commit, and will vary from repo to repo.  Perhaps a broad description of what is affected, such as "UI" and "API"?


Grammar
-------

Some commit guidelines are prescriptive about what grammar to use in commit subjects.  The two popular options are imperative mood (what will this commit do? "fix: handle name changes correctly") or past tense (what did this commit do? "fix: corrected the handling of name changes").


Tooling
-------

One of the advantages of formalized commit messages is using them as input to tooling and conformance checkers.  We will investigate tooling in the future, and are making no recommendations now.



Change History
==============

2021-03-16: Updates throughout based on review feedback.

2021-02-25: Converted to OEP-0051.

2021-02-01: A draft for review.


.. _Conventional Commits: https://www.conventionalcommits.org
.. _Change Transparency: https://github.com/edx/open-edx-proposals/pull/180
.. _Angular commit message format: https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format
