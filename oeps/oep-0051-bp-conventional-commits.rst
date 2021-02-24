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
     - 2021-02-24
   * - Authors
     - Ned Batchelder
   * - Arbiter
     - To Be Determined
   * - Status
     - Provisional
   * - Type
     - Best Practice
   * - Created
     - 2021-02-01
   * - Review Period
     - To Be Determined
   * - Resolution
     - TK
   * - References
     - TK


Abstract
========

Commits should be clearly labeled for their effect on consumers of the repository.  To do this, we adopt the `Conventional Commits`_ guidelines, which detail structured commit messages that clarify the impact of each commit.

This is part of our Change Transparency initiative.

Motivation
==========

Developers should describe the changes they make.  This information is valuable to many audiences: deployers, integrators, end-users, operators, and so on. Developers are in the best position to accurately describe their work and its implications.

The nearer to the change that information is captured, the easier it will be to write, and the more accurate it will be.  Changes made by developers are consumed by many people, from the pull request reviewer to the final user of the product.  The more the developer can explain the change, the more easily the change will be understood and used, and the more efficient the overall development process will be.

Commit messages are the closest text to the change itself.  The more
information-rich we can make commit messages, the more useful they will be.


Specfication
============

We are adopting the `Conventional Commits`_ spec, with our own adjustments.

.. note:: Many people are familiar with the `Angular commit message format`_, which uses conventional commits and inspired the Conventional Commits spec.  We are **not** adopting the Angular rules.

Git commit messages have these parts::

    <type>: <subject>

    <body>

    <footer>

Type
----

We use these commit types:

* **build**: anything to do with building or releasing the repo: Makefile, tox.ini, Travis, Jenkins, GitHub actions, and so on.

* **chore**: a repetitive mechanical task such as updating requirements or translations.

* **docs**: documentation-only changes. Documentation changes for other types of work should  be included in those commits.

* **feat**: a new feature, or a change to an existing feature. Anything that changes the set of features.

* **fix**: bug fixes, including changes to the behavior of features, and changes that remove or mitigate security vulnerabilities.

* **perf**: performance improvement.

* **refactor**: code reorganization that doesn't change behavior from the repo consumer perspective.

* **revert**: undo a previous change. The previous commit message, including the type prefix, is included.

* **style**: improve the styling of the code.

* **test**: test-only changes. Adds missing tests or corrects existing tests. Tests accompanying other types of changes go into those commits.

Breaking changes include an exclamation point as part of the type::

    feat!: removed the ability to author courses

Ideally, a single commit won't mix these types together.  If a commit does, use the most important type in the commit message.  The priority order for the types is generally: revert, feat, fix, perf, docs, test, build, refactor, style, chore.

Scope
-----

The `Conventional Commits`_ spec includes a parenthesized scope after the type.  Open edX repos are large and varied, making standardization of scopes difficult.  We won't use scopes for now.  This could change in the future.

Subject
-------

Commit message subjects should be short.  Put more information in the body of the commit message to fully explain your change.

Body
----

The subject of the commit is rarely enough information to fully understand the commit.  The body can contain as much information as you like.  Be generous.  Take a moment to think about what you would want to know if someone else had authored this commit.

The more information you can put in the body of the commit message, the better. It's not unreasonable to have two paragraphs of explanation in the body.

Breaking changes especially should have detailed information about the implications and alternatives.


Footer
------

We are not requiring any formalized footers in the commit message, though the use of  a ``BREAKING CHANGE:`` footer is encouraged as a way of providing more details about breaking changes.


Discussion
==========

Conventional Commits asks us to categorize changes into a small number of categories.  There will naturally be cases where it isn't obvious which commit type to use.

Choosing the commit type to use for a commit is important, but it's only the first step.  If you have doubts about which commit type to use, choose the highest-priority type that could apply.  Then write a detailed body explaining the full complexity of your change.

**feat vs fix**: some user-visible changes to features could be classified as "feat" or "fix".  Choose "feat" if the change adds to the set of features.  Choose "fix" if the change affects how a feature behaves.  Yes, this is still subjective.  

**breaking changes to features**: changing how a feature works is not a breaking change.  For example, users are sent to a new experience instead of the old experience. This is not a breaking change.  It should get a "feat" label, but not a "feat!" label.

**DEPR**: removing deprecated code likely is a breaking change.

**pinned dependencies**: updating the version of a pinned dependency seems like just a chore, but consider the repo consumer's perspective.  If an updated dependency adds a feature, then the one-line commit to update the pinned version should be marked "feat".

If you are interested to see other discussion about these sorts of questions, the `Conventional Commits repo issues`__ have a number of threads.

__ https://github.com/conventional-commits/conventionalcommits.org/issues


Tooling
=======

One of the advantages of formalized commit messages is using them as input to tooling and conformance checkers.  We will investgate tooling in the future, and are making no recommendations now.


Rationale
=========

The existing Conventional Commits standard is a familiar and widely adopted standard.  It's a good place to start our own guidelines.  It builds on our recently adopted pull request template to help focus developers on providing helpful information about their changes.

We've adapted the guidelines to our own projects, and started small.


Change History
==============

2021-02-25: Converted to OEP-0051.

2021-02-01: A draft for review.


.. _Conventional Commits: https://www.conventionalcommits.org
.. _Angular commit message format: https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format
