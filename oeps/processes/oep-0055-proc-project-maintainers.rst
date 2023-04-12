OEP-55: Project Maintainers
###########################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0055 <oep-0055-proc-project-maintainers>`
   * - Title
     - Project Maintainers
   * - Last Modified
     - 2022-05-13
   * - Authors
     - Edward Zarecor <e0d@axim.org>, Sarina Canelake
       <sarina@axim.org>, Xavier Antoviaque <antoviaque>, Olivia
       Ruiz-Knott <oliviaruizknott>, Ned Batchelder <nedbat>, David
       Joy <davidjoy>
   * - Arbiter
     - Jeremy Bowman <jbowman@2u.com>
   * - Status
     - Provisional
   * - Type
     - Process
   * - Created
     - 2022-01-28
   * - Review Period
     - 2022-03-21 - 2022-05-13
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - `Follow-up Work <https://openedx.atlassian.net/wiki/spaces/COMM/pages/3426844690/Maintainership+Pilot>`_

.. contents::
   :local:
   :depth: 3

Abstract
********

The term "maintainer" refers to an individual or group of people who serve as
leaders for a given component (a service or library comprised of code and hosted
on GitHub) of the Open edX platform. Maintainers provide both technical and
community management. This document defines what a maintainer is, how they are
chosen, and how they work together.

Overview
********

The Open edX platform is a service oriented platform for authoring and
delivering online learning based on the `Django framework`_.  At the
highest level, the platform is composed of numerous services (IDAs
[#ida]_), micro-frontends (MFEs [#mfe]_) and libraries.  The code for these
services and libraries is managed in git and hosted on the GitHub
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
open-source projects and widely considered a best practice [#maintainer]_.

The maintainer role is a leadership role that encompasses both technical and
community management elements.  Both dimensions of the role are equally
important. Maintainers demonstrate their fitness for this role by being part of
the Core Contributor program prior to their maintainer nomination.

All non-deprecated components of the Open edX project must have one
or more named maintainers.  When no such maintainer(s) exist, this will
be considered an urgent problem to solve.

This OEP proposes a formal definition of the role of maintainer that
will be open to all qualified Open edX community members.  Maintainers
will be responsible for the health of platform components, but not for
their performance in any particular community member's production
environment [#ops]_.

Motivation
**********

With the change in stewardship from edX to Axim Collaborative,
it is essential to have a well defined plan for maintaining
the platform.

In order to ensure that the project is sustainable for the long term,
maintenance must be shared by the entire community.  The project must
be robust enough that it can tolerate firms and contributors leaving
the ecosystem.

As a project we must acknowledge that maintenance is hard work, but
work that must be done for the platform to be fit for use.  The best
way of ensuring this is to have a named person or persons who are
responsible for getting the work done, a measurable definition of
"well-maintained," and feedback looks to ensure accountability and
continuous improvement of our processes.

Specification
*************

Definitions
===========

The term **repository** will mean a repository on the GitHub social
coding platform.

The term **component** will typically mean the unit of software housed
in a single GitHub repository.  Exceptions will be made in the case of
edx-platform where there may be multiple distinguishable components in
that single repository.  Components will typically, but not always, be
hosted at the `openedx GitHub organization`_. Components will be
either "part of" or "critical to" the open-source platform.

The term **maintainer** will mean a named person or persons
responsible for the health of the component and the component's community.
The remainder of this specification will be focused on the details of
the role.

The term **core contributor** will mean a named person participating
in the `Core Contributor Program`_ as a coding core contributor and
meeting the requirements of that program.

The term **maintainer scrum-of-scrums** will mean the group of all
maintainers.  They are responsible for flagging issues to project
leadership and proposing process improvements for component
maintenance.

The term **Technical Oversight Committee** (TOC) will mean the group
responsible for the overall oversight and stewardship of the Open edX
project. The TOC itself was established and is governed by a formal charter.
You can read the full document `here
<https://openedx.org/wp-content/uploads/2021/12/TCRIL-Technical-Oversight-Committee-Charter-rev.11-16.21.pdf>`_.

Historical Context
==================

Historically, the Open edX project has not had a well-defined role
of maintainer.  At times, community members stepped into this role
informally.  The role was also part of a broader "owner" role at edX.
Owners were also responsible for the health of the component they
owned both in the repository and in production; owners answered the
pager when something went wrong.

What Maintainers Do
===================

The role of the repository or project maintainer is both a community
focused and a technical role [#role]_.  Maintainers will be the public face
of each component and should be welcoming and available.  While
maintainers should strive to always be friendly and kind in their
communication, firmness and directness are equally important.
Maintainers must enforce the project's standards.  In cases where a
proposed change isn't aligned with the our standards or direction, it
is best to make that clear as early as possible.

Non-technical responsibilities of maintainers include:

* Effective communication with community members, other maintainers, and project
  leadership
* Selection and approval of Core Contributors with commit rights to their
  repositories, by participating in the comment period for Core Contributor
  nominees nominated to commit code to their repo(s), as part of the `Core
  Contributor Program`_
* Disciplined management to ensure that maintenance work is done
* Enforcement of the project's technical standards
* Ensuring that all work related to the component, planning, design, and
  development occurs in public spaces, enabling all Open edX community members
  to read and comment on discussions. Discretion may be used in cases that might
  need to remain private (such as security disclosures)
* Encouraging kindness and friendliness in project discourse, but not at the
  expense of enforcing the project's technical standards. At minimum,
  maintainers should ensure that all discussion follows the `Code of Conduct`_
* Ensuring that bug reports are read, acknowledged, and prioritized in a timely
  fashion

Technical responsibilities of maintainers include:

* Understanding the current component architecture and implementation
* Ensuring that basic documentation for the component exists and is accurate
* The ability to solve or escalate issues facing users of the component
* Ensuring that dependencies are updated as needed to avoid security risks or use
  of components that or not fit for use, e.g., unsupported, not maintained, or
  at their end-of-life.
* Ensuring ongoing compatibility with other components of the Open edX ecosystem
  as they are developed
* Having a documented technical vision for the evolution of the component
* The ability to promptly triage incoming requests that propose changes to or
  extensions of the component, assessing their appropriateness and/or routing
  them to proper reviewers
* Understanding the role of the component in the overall platform and how it
  interacts with other components
* Must be available to participate in the comment period for Core Contributor
  nominees who have been nominated to commit code to the maintainer's repo(s),
  as part of the `Core Contributor Program`_

See `Jobs of the Maintainer`_ for more details on these requirements.

It is acceptable for a component to be maintained by a team or group as long as
that arrangement does not reduce the reachability of at least one maintainer or
reduce accountability for the above responsibilites. The maintaining team must
have ways of direct contact, such as an email address and a GitHub team, that
anyone can use to get ahold of someone - it is completely acceptable for the
team to figure out arrangements for list monitoring, such as an "on-call"
rotation.

Prerequisites
=============

In order to be a repository maintainer, you must

* Be an active member of the Open edX community who has made technical
  contributions to the project in the last 6 months, such as feature work,
  upgrades, bug fixes, or architectural/design contributions
* Abide by the Open edX `Code of Conduct`_
* Be a member of the `Core Contributor Program`_ with rights to the repository
  in question who has demonstrated excellence in the role of Core Contributor.
  From time-to-time, in the case of a strong candidate and project need, core
  contributor status and maintainer status may be conferred at the same time
* In the case of repos being moved into the `openedx GitHub organization`_, be
  an original maintainer of the code

We value the level of commitment that comes with being a Core Contributor and
think it is a necessary pre-requisite to becoming a Maintainer. We want to
ensure that the programs are congruent and not working at cross purposes; hours
spent maintaining a component will count as Core Contributor hours. If we find
that the requirement of being a Core Contributor limits participation in the
Maintainers program, we may choose to revisit this requirement.

Bootstrapping
=============

Upon acceptance of this OEP, a bootstrapping period, starting with a small pilot
phase, will occur whereby 2U staff and community members will formally take on
maintainership roles. Details of this period will be documented in the
`maintainership documents`_ on the Open edX wiki, due to their rapidly changing
nature.

The Maintainer Scrum-of-Scrums
==============================

In order to coordinate certain maintainer responsibilities including
voting on nominated maintainers, iterating on standards, and removing
maintainers, minimal mechanisms for coordination are required.

For now, we will assume that maintainers will self-organize to
complete the critical activities.

Coordination will typically be over asynchronous channels.

From time-to-time, a task force may be required to complete certain
project-wide responsibilities like developing repository standards,
as well as tools for measuring compliance to those standards.

Becoming a Maintainer
=====================

After the bootstrapping phase, the Open edX project will need
processes for adding maintainers, replacing maintainers, and ensuring
that new components are maintained.

Becoming a maintainer requires fulfilling the `prerequisites`_
outlined above.

A new maintainer can be added in any of the following ways:

* An existing maintainer or core contributor can request that a new person
  become a component maintainer either in conjunction with the existing
  maintainer or by replacing them.
* If the component is sufficiently complex to justify multiple
  maintainers, those maintainers form a group and should organize
  internally and present a single point of contact (like
  teams-as-maintainers above).

In both of the above cases, the request for maintainership change should be
publicized in Discourse with a comment period not less than two weeks. All
community members are welcome to voice their support ("liking" the post does not
count), or comment with question/concerns. Existing maintainers and core
contributors may object to the nomination. If there is no objection from
existing maintainers or core contributors at the end of the comment period, the
role will be conferred.

We are not currently limiting staff of companies from appointing,
nominating, or voting for other employees of their company.  This
would be impractical as, currently, single companies are locus of
component maintenance work. This may be addressed in future revisions
to this proposal.

New Components
==============

In the case of new repositories, there should be a designated maintainer named
before the repository is created.

Unmaintained Components
=======================

In the case of unmaintained components, nominations will be made to
the scrum-of-scrums to fill the vacancy.  Nominations can be made by
any member of the community including the nominee themselves.  If
there is no objection from existing maintainers, the role will be
conferred.

If consensus cannot be reached lazily [#lazy_concensus]_, a vote of existing maintainers
will be taken.  Maintainers can approve a nomination with the approval
of at least three quarters of the members.

Removing a Maintainer
=====================

From time-to-time it will be necessary to remove a component
maintainer.  This might happen because:

* A maintainer leaves the project or changes employment
* A maintainer does not have the time or interest to continue in the
  role
* A maintainer is unreachable
* A maintainer is not fulfilling the responsibilities of the role

Trigger events for removing a maintainer would be:

* A maintainer informs the project, the scrum-of-scrums, and/or the `program
  administrators`_ that they can no longer continue in the role
* A maintainer has been unreachable for more than 30 days
* A maintainer has violated the project `Code of Conduct`_
* A maintainer hasn't met project quality standards consistently
  despite feedback

In the case that a maintainer has violated the `Code of Conduct`_, the
Goverance Team, specified in the Code of Conduct, will decide what the
repercussions of the violation will be, including whether the violator
can continue in the role of maintainer.  It should be noted that
maintainers are expected to be exemplars and, as such, will be held to
a high standard.

In the case that a maintainer is not meeting project quality standards
consistently, the scrum-of-scrums will inform the maintainer with the
expectation that their components meet standards within a calendar
month.  If, after a month, the quality issues are not resolved, the
maintainer will be removed by the scrum-of-scrums.  Additionally, a
maintainer who is warned three (3) times in a single calendar year
will also be removed. The `program administrators`_ serve as a
backstop - any community member may contact them at any time if
a problem arises and the normal channels of communication are
not working.

In this situation, once the existing maintainer is removed, the component enters
an "unmaintained" status. This status is to recognize that there is not another
group with unlimited spare capacity to pick up the slack, and to encourage the
community to find a new maintainer.

Documenting Maintainers
=======================

As it is essential that maintainers are discoverable and reachable, the person
or persons responsible for maintaining a component should be listed in the
repository.  Per :doc:`the backstage ADR
<oep-0055/decisions/0001-use-backstage-to-support-maintainers>` we will use a
``catalog-info.yaml`` file to capture the github handles for the maintainers of
a repository.

Jobs of the Maintainer
======================

Many of the day-to-day specifics of the maintainer role are outside of
the scope of this document.  For example, here we will note that
maintainers are responsible for ensuring that repositories meet
project standards, but not the details of those standards.  Those
standards - including establishing a channel for feedback, and details on how
and when that channel will be monitored - will be codified in an OEP over time,
but will initially be defined in the wiki page `requirements for public
repositories`_ to allow rapid iteration.

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
* **Technical Vision**: Maintainers should have a documented technical
  vision for their components. This vision should minimally answer the
  following questions:

  * What is the component for?
  * How it fits into the overall platform?
  * What are its limitations and key areas of debt?
  * What new patterns or technologies should be adopted to improve the
    component?
  * How it can be extended?

* **Project Documentation**: Maintainers provide descriptive project information
  in the following documents:

  * A required ``README`` file.  The specification of the README file is defined
    in :doc:`ADR 3 <oep-0055/decisions/0003-readme-specification>`.

  * We additionally recommend documentation to help others understand the
    component, such as:

    * ADRs
    * Architecture diagrams and architecture documentation
    * A product roadmap
    * Links to a project board

* **Continuous Improvement**: Maintainers should be thinking
  about continuously improving both software and processes.

Governance Structure
********************

The Maintainers program will be run by the maintainers themselves, working
together with input from Core Contributors and the broader community. The
`program administrators`_ will serve as a backstop for any questions or issues
about the Program or governance thereof that cannot be resolved by the
maintainers themselves. The Technical Oversight Committee (TOC) will be the
arbiter of last resort for governance matters.

.. _program administrators:

Contact the Program Administrators
**********************************

Questions about the Program can be directed to the Axim administrator(s) via
the ``#ask-axim`` Slack room in the `Open edX Slack
<https://openedx.slack.com/>`_. Questions or concerns of a more sensitive nature
may be directed to ``maintainer-program-admins@axim.org``.

Related Decisions
*****************

.. toctree::
   :glob:
   :maxdepth: 1

   oep-0055/decisions/*

Change History
**************

01-28-2022 - `Document created <https://github.com/openedx/open-edx-proposals/pull/290/>`_


Source Material
***************

* `What Makes a Great Maintainer of Open Source Projects?
  <https://www.computer.org/csdl/proceedings-article/icse/2021/029600a982/1sEXoQoeO0E>`_
  (`Alternative Link <http://gustavopinto.org/lost+found/icse2021.pdf>`_)

* `Collective Code Construction Contract <https://rfc.zeromq.org/spec/42/>`_
* `Best Practices for Maintainers <https://opensource.guide/best-practices/>`_
* `Producing OSS <https://producingoss.com/en/producingoss-letter.pdf>`_

Notes
*****

.. [#ida] An IDA is an Independently Deployable Application, a
       server-side service oriented application, typically built upon
       the Django framework.

.. [#mfe] An MFE is a Micro-Frontend, a discreet, client side
       application written in Javascript, typically using the ReactJS
       framework.

.. [#maintainer] The general acceptance of needing maintainers for OSS projects
       can be seen in much literature that starts from the assumption
       that maintainers are essential and instead asks, what makes a
       maintainer great, what are the best-practices maintainers
       should follow, how do we ensure maintainer don't burn out, how
       do we ensure critical parts of the software supply chain, like
       OpenSSL are maintained, etc.  I have included some revelant
       materials in the **Source Material** section.

.. [#ops] Needless to say, there must be feedback loops between
       maintainers and operators of the platform and, in some cases, a
       single person will perform both roles.  Maintainers should
       certainly be concerned about the performance characteristics of
       their components and committed to their scalability.

.. [#role] In fact, studies of the traits of effective maintainers have
       highlighted that non-technical aspects of the role are often
       more highly valued by community members. See `What
       Makes a Great Maintainer of Open Source Projects?
       <https://www.computer.org/csdl/proceedings-article/icse/2021/029600a982/1sEXoQoeO0E>`_
       (2021 IEEE/ACM 43rd International Conference on Software
       Engineering (ICSE))(`Alternative Link <http://gustavopinto.org/lost+found/icse2021.pdf>`_).

.. [#lazy_concensus] Lazy concensus is a process by which decisions
       are approved unless there are objections.  It is sometimes
       described as the "silence is concent" method.  Lazy concensus
       reduces ceremony and overhead and thus increases spead.  It is
       essential, however, that decisions to be made are published.
       There should be a review period that is commensurate with the
       gravity of the decision at hand.  Lazy concensus is used by
       other open-source projects and foundations including the
       `Apache foundation <https://community.apache.org/committers/lazyConsensus.html>`_

.. _Django framework: https://www.djangoproject.com/

.. _Core Contributor Program: https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0054-core-contributors.html

.. _maintainership documents: https://openedx.atlassian.net/wiki/spaces/COMM/pages/3426418790/Maintainership+of+Open+edX+Repos

.. _requirements for public repositories: https://openedx.atlassian.net/l/c/j6qLr5ET

.. _Code of Conduct: https://openedx.org/code-of-conduct/

.. _openedx GitHub organization: https://github.com/openedx/
