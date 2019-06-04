=========================================
OEP-14: Archiving edX GitHub Repositories
=========================================

+---------------+----------------------------------------------------------+
| OEP           | :doc:`OEP-14 </oeps/oep-0014-proc-archive-repos.rst>`    |
+---------------+----------------------------------------------------------+
| Title         | Archiving edX GitHub Repositories                        |
+---------------+----------------------------------------------------------+
| Last Modified | 2017-01-18                                               |
+---------------+----------------------------------------------------------+
| Author        | Christina Roberts <christina@edx.org>                    |
|               | Feanil Patel <feanil@edx.org>                            |
+---------------+----------------------------------------------------------+
| Arbiter       | Nimisha Asthagiri <nasthagiri@edx.org>                   |
+---------------+----------------------------------------------------------+
| Status        | Accepted                                                 |
+---------------+----------------------------------------------------------+
| Type          | Process                                                  |
+---------------+----------------------------------------------------------+
| Created       | 2017-01-09                                               |
+---------------+----------------------------------------------------------+
| `References`  | `ORA PR Discussion`_,                                    |
|               | `Initial Archiving Discussions`_                         |
+---------------+----------------------------------------------------------+

.. _ORA PR Discussion: https://github.com/edx/edx-ora/pull/187
.. _Initial Archiving Discussions: https://openedx.atlassian.net/wiki/display/IT/Proposed+Github+Deprecation+Process

Abstract
========

The `edx organization`_ contains a large number of repositories, most of
which are active and maintained, but some of which are now obsolete. To
clarify the status of repositories, a process for archiving a
repository is defined below.

.. _edx organization: https://github.com/edx

Motivation
==========

Recently openedx.yaml files were added to edX repositories per `OEP-2`_. In the
course of deciding owners for those repositories, there was an `ORA PR
Discussion`_ about how best to handle deprecated or obsolete repositories. In
particular, do obsolete repositories need owners, and how can repositories be
clearly marked as present for archive purposes only?

This discussion resurfaced related to edX's usage of Gemnasium to report
the usage of third-party libraries that have known security issues. All
repositories under the edX organization were being monitored, but this added
noise when trying to understand the number of third-party library updates
required for actively maintained repositories.

.. _OEP-2: https://open-edx-proposals.readthedocs.io/en/latest/oep-0002.html


Specification
=============

When a repository under the `edx organization`_ will no longer be maintained
because it is no longer in use, the following steps should be followed.


Transfer to New Owner if Interest
---------------------------------

First, if the repository is public, and a part of Open edX releases, follow
these steps to see if anyone would like to take ownership of it:

1. Send an email to edx-code@googlegroups.com announcing that the repository
   will be archived, and inquiring if anyone would like to take ownership of
   the repo. If there are no responses after 2 work days, skip to `Archive
   Steps`_.

2. If someone does wish to take ownership of the repository, email the internal
   edX developers mailing list to see if there are any objections. If there are
   no objections after 2 work days, post to edx-code@googlegroups.com that the
   transfer will take place.

3. Create an IT help ticket for the repository to be transferred to the new
   organization.

4. Once the transfer has occurred, create a fork of the transferred repository
   into the `edx organization`_ and follow the `Archive Steps`_ below for the
   forked repo.


Archive Steps
-------------

1. Update the README.rst file in the repository to state that it is archived,
   using the `README Archive Statement`_ below.

2. Add ``archived: True`` to openedx.yaml (creating the file if necessary). Note
   that it is not necessary for the openedx.yaml file to define an owner when
   archived is set to True.

3. Create an IT help ticket to update the description of the repository to
   begin with ``[ARCHIVED]`` and for the repository to be archived per `GitHub's
   archive process`_

.. _GitHub's archive process: https://help.github.com/en/articles/archiving-repositories


README Archive Statement
------------------------

Include this statement in the README.rst file:

    This repository has been archived and is no longer supported—use it at your
    own risk. This repository may depend on out-of-date libraries with security
    issues, and security updates will not be provided. Pull requests against
    this repository will also not be merged.


For Repos that are Forks
------------------------

If the repository is a fork of an upstream repository that is not within the
edX organization, and will no longer be maintained, it can be deleted with the
following steps.

1. Review any differences between the upstream parent and the forked repo.
   Capture anything that should be kept for historical purposes.

2. Ensure that there are no edX dependencies on this repository.

3. Submit an IT help ticket to have it deleted.


Rationale
=========

The proposed process leverages the already-existing ``archived`` flag in
openedx.yaml. It does not require introducing a new organization that is
maintained by edX, and the source code remains easily visible and searchable
(see `Rejected Alternatives`_).


Backward Compatibility
======================

This proposal does not introduce any backward compatibility issues.


Reference Implementation
========================

The `Discussions Hackathon repository`_ has been updated to conform to the
`Archive Steps`_.

.. _Discussions Hackathon repository: https://github.com/edx/discussions


Rejected Alternatives
=====================

There are a couple variations of this proposal that were originally discussed in
`Initial Archiving Discussions`_. Many of the steps of updating documentation
and notifying the open source community are essentially the same; the major
differences from the proposed process are outlined below.


Alternative 1: Transfer Repository
----------------------------------

Transfer the obsolete repository to a new organization: edx-archived.

Pros:

- `edx organization`_ is no longer littered with unsupported/obsolete
  repositories.
- GitHub search results within the `edx organization`_ do not include matches
  in archived repositories. This could decrease confusion, especially since
  repo descriptions are not included in search results.
- Gemnasium monitoring may cease automatically (although this would need to be
  confirmed).
- Pattern followed by `Facebook`_, and thus might be familiar to others.

Reasons rejected:

- This creates another organization that edX must maintain and adds
  administrative overhead.
- It could be difficult for people to find the code through search, though
  forwarding links would work for anyone who already linked to the repositories.

.. _Facebook: https://github.com/facebookarchive


Alternative 2: Create Archive Branch
------------------------------------

Move the code from the master branch to an archived branch,
while leaving the repository itself within `edx organization`_.

Pros:

- No need to create and maintain a new organization.
- Gemnasium monitoring will cease automatically.
- No help tickets to IT or DevOps are required.
- This pattern was recommended on `Anselm Hannemann's blog`_, though it is not
  known how many organizations (if any) have adopted this process.

Reasons rejected:

- Non-intuitive, and could be confusing for developers to understand the state
  of the code, as cloning the repo or viewing it on GitHub would show an empty
  repository (Note: this could possibly be improved by changing the default
  branch for the repository, but that might reintroduce the Gemansium monitoring
  issue).
- It is unclear what the implications would be for any existing forks.

.. _Anselm Hannemann's blog: https://helloanselm.com/2013/handle-deprecated-unmaintained-repositories/


Change History
==============

2017-01-18
----------

* Original publication

2017-05-23
----------

* Added steps for repositories that live in the edX org, but are forks of other, independent repositories

2019-05-16
----------

* Updated to use GitHub's archive capability.
* Don't ask the community about public repos in the edx org that are not a part of Open edX.
