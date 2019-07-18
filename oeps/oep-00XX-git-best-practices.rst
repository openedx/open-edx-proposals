======================
OEP-XXXX: OEP Template
======================

.. This OEP template is based on Python's PEP standard.

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-XXXX </oeps/oep-XXXX-bp-git-best-practixes`  |
+-----------------+--------------------------------------------------------+
| Title           | Git best practices                                     |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2019-07-18                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Joseph Mulloy                                          |
|                 |                                                        |
+-----------------+--------------------------------------------------------+
| Arbiter         | <Arbiter's real name and email address>                |
+-----------------+--------------------------------------------------------+
| Status          | Draft                                                  |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2019-07-18                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | <start - target end dates for review>                  |
+-----------------+--------------------------------------------------------+
| `Resolution`    | <links to any discussions where the final              |
|                 | status was decided>                                    |
+-----------------+--------------------------------------------------------+
| `References`    | <links to any other relevant discussions               |
|                 | or relevant related materials>                         |
+-----------------+--------------------------------------------------------+

Abstract
========

This OEP outlines git best practices that if followed increase the usability
of the git commit history.

Motivation
==========

Git history is often used to understand the motivations and context in which a
change was made. When commit messages are descriptive and link to a ticket it's
possible to understand why a change was made. When the message is terse it's
much more difficult to understand the change.

Specification
=============

The specification describes the technical details of the Architecture, Best
Practice or Process proposed by the OEP. If the proposal includes a new API,
specify its syntax and semantics.

* Commit messages
** The first line should be no longer than 50 characters
** The second line should be no longer than 72 characters
** The first line should concisely describe the reason for the change
** The first line should include the JIRA Issue ID, if there is a JIRA Issue
* Branch names
** Feature branch names should be prefixed with the creating user's github
   username and a slash
** If there is an associated JIRA Issue the Issue ID should be next before
   another slash
** Example: username/proj-1234/descriptive name

# TODO: Good and Bad Examples?

Rationale
=========

* https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

The character limits affect the git command line interface as well as GitHub.
When creating a Pull Request, GitHub will automatically use the first line of
the commit message as the PR title if there is a single commit on the branch.
If the first line is longer than 50 characters GitHub will truncate the line and
put the rest of it in the PR description. This makes it harder to read and
sometimes the more relevant part of the message is past the cutoff, so the PR
title is not very useful.

Backward Compatibility
======================

There should be no backwards complatibility concerns, git history can not be
edited, therefore this OEP will only apply going forward.

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
