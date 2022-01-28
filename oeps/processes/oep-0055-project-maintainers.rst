=============================
OEP-0055: Project Maintainers
=============================

.. This OEP template is based on Python's PEP standard.

.. list-table::
   :widths: 25 75

   * - OEP
     - Link to the doc in the following format::

        :doc:`OEP-0055 <oep-0055-proc-Project Maintainers>`

        * <XXXX is the next available OEP number>
        * <YYYY is the abbreviated Type: proc | bp | arch>
        * <ZZZZ is a brief (< 5 words) version of the title>

   * - Title
     - Project Maintainers
   * - Last Modified
     - 2022-01-28
   * - Authors
     - Edward Zarecor <e0d@tcril.org>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - Draft
   * - Type
     - Process
   * - Created
     - 2022-01-28
   * - Review Period
     - <start - target end dates for review>
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - <links to any other relevant discussions or relevant related materials>

Abstract
========

The Open edX platform is a service oriented platform for authoring and
delivering online learning based on the `Django framework`_.  At the
highest level, the platform is composed of numerous services (IDAs
[#]_), micro-front ends (MFEs [#]_) and libraries.  The code for these
services and libraries is managed in git and hosted on GitHub
platform.

Each component part of the Open edX platform requires maintenance to
ensure that it meets, and continues to meet, the project definition of
high quality.  That definition will include project standards,
appropriate documentation, and fitness for use, among other things.
That definition will evolve as project needs do and we learn what
works and what doesn't.

Component maintenance is work.  To ensure that that work is done, a
person or persons must be recognized as responsible for doing it,
and held accountable for its performance.

The Open edX project, like many projects, calls this role
"maintainer."  The notion of a project maintainer is typical of
open-source projects and widely considered an best practice [#]_.

The maintainer role is a leadership role that encompasses both
technical and community elements.  Both dimensions of the role are
equally important.

All, non-deprecated, components of the Open edX project must have a
named maintainer.  When no such maintainer exists, this will be
considered an urgent problem to solve.

This OEP proposes a formal definition of the role of maintainer that
will open to all qualified Open edX community members.  Maintainers
will be responsible for the health of platform components, but not for
their performance in production [#]_.

Motivation
==========

With the change in stewardship from edX to The Center for Reimagining
Learner, it is essential to have a well defined plan for maintaining
the platform.

In order to ensure that the project is sustainable for the long term,
maintenance must be shared by the entire community.  The project must
be robust enough that a it can tolerate firms and contributors leaving
the ecosystem.

As a project we must acknowledge that maintenance is hard work, but
work that must be done for the platform to be fit for use.  The best
way of ensuring this is to have a named person or persons who are
responsible for getting the work done.

Specification
=============

Definitions
-----------

The term **repository** will mean a repository on the GitHub social
coding platform.

The term **component** will typically mean the unit of software housed
in a single GitHub repository.  Exceptions will be made in the case of
edx-platform where there may be multiple distinguishable components in
that single repository.  Components will typically, but not always, be
hosted at the Open edX GitHub organization.  Components will be "part
of" or "critical to" the open-source platform.

The term **maintainer** will mean a named person responsible for the
health of component and the component's community.  The remainder of
this specification will be focused on the details of the role.

The term **core contributor** will mean a named person participating
in the `Core Contributor Program`_ and meeting the requirements of
that program.

The term **maintainer scrum-of-scrums** will mean group of all
maintainers who are responsible for flagging issues to project
leadership and proposing process improvements for component
maintenance.

The term **Technical Oversight Committee** (TOC) will mean the group
responsible for the overall oversight and stewardship of the Open edX
project. The TOC itself was established and is governed by a formal
charter.  You can read the full document `here
<https://openedx.org/wp-content/uploads/2021/12/TCRIL-Technical-Oversight-Committee-Charter-rev.11-16.21.pdf>`_.

Context
-------
Historically, the Open edX project has not had a well-defined role
of maintainer.  At times, community members stepped into this role
informally.  The role was also part of a broader "owner" role at edX.
Owners were also responsible for the health of the component they
owned both in the repository and in production; owners answered the
pager when something went wrong.

The role of the repository or project maintainer is both a community
focused and a technical role[#]_.  Maintainers will be the public face
of each component and should be welcoming and available.

Non-technical responsibilities of maintainers include:

* Effective communication with community members, other maintainers,
  and project leadership
* Disciplined management to ensure that maintenance work is done
* Enforcement of the project's standards
* The ability to solve or escalate issues facing users of the
  component

Technical responsibilities of maintainers include:

* Understanding the current component architecture and implementation
* Having a technical vision for the evolution of the component
* The ability to assess the appropriateness of proposed changes to or
  extensions of the component
* The role of the component in the overall platform and how it
  interacts with other components

It is acceptable for a component to be maintained by a team or group
as long as that arrangement does not reduce the reachablility of the
maintainer or reduce accountability.

Prerequisites
-------------
In order to be a repository maintainer, you must

* Be an active member of the Open edX community who has contributed to
  the project in the last 6 months.  The types of contributions that
  are relevant will match those defined in the `core contributor
  program`_
* Abide by the `Open edX Code of Conduct
  <https://openedx.org/code-of-conduct/>`_
* Be a core contributor for the repository in question who has
  demonstrated excellence in the role of core contributor.  From
  time-to-time, in the case of a strong candidate and project need,
  core contributor status and maintainer status may be conferred at
  the same time.

Bootstrapping
-------------
In order to ensure an orderly roll-out of the formal maintainer role,
existing 2U staff in the owner role will transition into the role of
maintainer for the components that they own.

Community members who are acting in informal maintainer roles, will
also be asked to become named maintainers.

A list of all components without maintainers will be created and
published.

An asynchronous maintainer scrum-of-scrums will be created as a Slack
channel.

Standards for measuring the quality of repositories will be rolled out
across all repositories.  These standards can benefit from work done
at edX to measure `repository health <https://github.com/openedx/edx-repo-health>`_.

Becoming a Maintainer
---------------------
After the bootstrapping phase, the Open edX project will need
processes for adding additional maintainers, replacing maintainers,
and ensuring that new components are maintained.

**This section should be congruent with the Core Contributor process**

A new maintainer can be added in any of the following ways:

* An existing maintainer can request that a new person become a
  component maintainer either in conjunction with the existing
  maintainer or replacing them.  If there is no objection from
  existing maintainers, the role will be conferred.
* In the case of vacancies, the nominations will be made to the
  scrum-of-scrums to fill the vacancy.  If there is not objection from
  existing maintainers, the role will be conferred.
* If there consensus cannot be reached,
* In the case of new repositories, there should be a designated
  maintainer named before the repository is created.

Removing a Maintainer
---------------------
From time-to-time it will be necessary to remove a component
maintainer.  This might happen because:

* A maintainer leaves the project or changes employment
* A maintainer does not have the time or interest to continue in the
  role
* A maintainer is unreachable
* A maintainer is not fulfilling the responsibilities of the role

Trigger events for removing a maintainer would be:

* A maintainer informs the project that the can no longer continue in the role
* A maintainer has been unreachable for more than 30 days
* A maintainer has violated the project `Code of Conduct`_
* A maintainer hasn't met project quality standards consistently despite feedback
  
Jobs of the Maintainer
----------------------

Many of the day-to-day specifics of the maintainer role are outside of
the scope of this document.  For example, here we will note that
maintainers are responsible for ensuring that repositories meet
project standards, but not the details of those standards.  Those
standards will be codified in an OEP over time, but will initially be
defined in the wiki page `requirements for public repositories`_ to
allow rapid iteration.

The key aspects of the maintainer role fall into the following
categories

* **Community Stewardship**: Maintainers are representatives of the
  project in the community and are responsible for contributing to an
  environment that is welcoming, vibrant, and growing.
* **Project Management**: Maintainers are responsible for ensuring
  that critical maintenance is done, but this doesn't mean doing it all
  themselves. Effective delegation is an important maintainer skill.
* **Quality Assurance**: Maintainers are responsible for the quality
  of their components.  Quality will mean meeting defined project
  standards.  It can also mean improving our standards or improving
  our processes.
* **Technical Vision**: Maintainers should have a technical vision for
  their components. This would include key areas of debt that require
  payoff, a new pattern that should be adopted to improve the
  component design, a path to add new features user need, and more.
* **Continuous Improvement**: Maintainers should be thinking
  about continuously improving both software and processes.




Change History
==============

A list of dated sections that describes a brief summary of each revision of the
OEP.


Source Material
===============

* `What Makes a Great Maintainer of Open Source Projects? <https://www.computer.org/csdl/proceedings-article/icse/2021/029600a982/1sEXoQoeO0E>`_
* `Collective Code Construction Contract <https://rfc.zeromq.org/spec/42/>`_

Notes
=====

.. [#] An IDA is an Independently Deployable Application, a server-side service oriented application, typically built upon the Django framework.

.. [#] An MFE is a Micro-Front end, a discreet, client side application written in Javascript, typically using the ReactJS framework.

.. [#] TODO: Add sources for best practices claim.

.. [#] Needless to say, there must be feedback loops between
       maintainers and operators of the platform and, in some cases, a        single person will perform both roles.

.. [#] In fact, studies of the traits of effective maintainers have highlighted that non-technical aspects of the role are often more highly valued by community members 2021 IEEE/ACM 43rd International Conference on Software Engineering (ICSE) `What Makes a Great Maintainer of Open Source Projects? <https://www.computer.org/csdl/proceedings-article/icse/2021/029600a982/1sEXoQoeO0E>`_



.. _Django framework: https://www.djangoproject.com/

.. _Core Contributor Program: https://github.com/openedx/open-edx-proposals/pull/275

.. _requirements for public repositories: https://openedx.atlassian.net/l/c/j6qLr5ET

.. _Code of Conduct: https://openedx.org/code-of-conduct/

