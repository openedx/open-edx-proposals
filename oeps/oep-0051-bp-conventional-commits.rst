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
     - Feanil Patel
   * - Status
     - Draft
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

.. todo:
   what type for:
    - commit that removes a depr'd thing
    - commit that updates a pinned package to get new features?


Abstract
========

Commits should be clearly labeled for their effect on consumers of the repository.  To do this, we adopt the `Conventional Commits`_ guidelines, which detail structured commit messages that clarify the impact of each commit.

This is part of our Change Transparency initiative.

Motivation
==========


Specfication
============

We are adopting the `Conventional Commits`_ spec, with our own adjustments.

.. note:: Many people are familiar with the `Angular commit message format`_, which uses conventional commits and inspired the Conventional Commits spec.  We are not adopting the Angular rules.

Commit messages have these parts::

    <type>: <subject>

    <body>

    <footer>

Type
----

We use these commit types:

* **build**: anything to do with building or releasing the repo: Makefile, tox.ini, Travis, Jenkins, GitHub actions, and so on.
* **chore**: a repetitive task such as updating requirements or translations.
* **docs**: documentation-only changes. Documentation changes for other types of work can be included in those commits.
* **feat**: a new feature, or a change to an existing feature. Anything that changes the set of features.
* **fix**: bug fixes.
* **perf**: performance improvement.
* **refactor**: code reorganization that doesn't change behavior.
* **revert**: undo a previous change. The previous commit message, including the type prefix, is included.
* **style**: improve the styling of the code.
* **test**: test-only changes. Adds missing tests or corrects existing tests. Tests accompanying other types of changes go into those commits.

Breaking changes include an exclamation point as part of the type::

    feat!: removed the ability to author courses

Ideally, commits don't mix these types together.  If they do, use the most important type in the commit message.  The priority order for the types is generally: revert, feat, fix, perf, docs, test, build, refactor, style, chore.

Scope
-----

The `Conventional Commits`_ spec includes a parenthesized scope after the type.  Open edX repos are large and varied, making standardization of scopes difficult.  We won't use scopes.  This could change in the future.

Subject
-------

Commit message subjects should be short.  Put more information in the body of the commit message to fully explain your change.

Body
----

The subject of the commit is rarely enough information to fully understand the commit.  The body can contain as much information as you like.  Be generous.  Take a moment to think about what you would want to know if someone else had authored this commit.


Footer
------

We are not proposing any formalized footers in the commit message.


Tooling
=======

One of the advantages of formalized commit messages is using them as input to tooling and conformance checkers.  However, we are not proposing the use of these tools at the moment.


Rationale
=========

The rationale adds to the specification by describing the events or
requirements that led to the proposal, what influenced the design, and why
particular design decisions were made. The rationale could provide evidence
of consensus within the community and discuss important objections or
concerns raised during discussion. It could identify any related work, 
for example, how the feature is supported in other systems.

Backward Compatibility
======================

This statement identifies whether the proposed change is backward compatible.
An OEP that introduces backward incompatibilities must describe the
incompatibilities, with their severity and an explanation of how you propose to
address these incompatibilities.

Reference Implementation
========================

The reference implementation must be completed before any OEP is given "Final"
status, but it need not be completed before the OEP is "Accepted". While there is
merit to the approach of reaching consensus on the specification and rationale
before writing code, the principle of "rough consensus and running code" is
still useful when it comes to resolving many discussions.

Rejected Alternatives
=====================

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

Change History
==============

A list of dated sections that describes a brief summary of each revision of the
OEP.


.. _Conventional Commits: https://www.conventionalcommits.org
.. _Angular commit message format: https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format
