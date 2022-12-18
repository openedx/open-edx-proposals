
OEP-62: Working Groups
######################

.. list-table::
   :widths: 25 75

   * - OEP
     - Link to the doc in the following format::

        :doc:`OEP-XXXX <oep-XXXX-YYYY-ZZZZ>`

        * <XXXX is the next available OEP number>
        * <YYYY is the abbreviated Type: proc | bp | arch>
        * <ZZZZ is a brief (< 5 words) version of the title>

   * - Title
     - <OEP title>
   * - Last Modified
     - <date string, in YYYY-MM-DD format>
   * - Authors
     - <list of authors' real names and optionally, email addresses>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - <Draft | Under Review | Deferred | Provisional | Accepted | Rejected | Withdrawn | Final | Replaced>
   * - Type
     - <Architecture | Best Practice | Process>
   * - Created
     - <date created on, in YYYY-MM-DD format>
   * - Review Period
     - <start - target end dates for review>
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - <links to any other relevant discussions or relevant related materials>
Abstract
********

Working groups are voluntary associations of Open edX community members formed around some aspect of the project. These groups are organically formed and dissolved by consensus amongst those interested. While they must follow a few basic community guidelines, working groups have autonomy to decide their own structure and operating procedures.

Motivation
**********

The motivation is critical for OEPs that will change any part of the Open edX
ecosystem. Explain why the existing architecture or process is inadequate to
address the problem that the OEP solves, or why adopting the best practice
would significantly improve the Open edX world.


Specification
*************

What is a working group?
========================

An *Open edX Working Group* is any group of community members that follows the following guidelines:

Consensus
---------

Decisions made by a working group should be made by group *consensus*. The way consensus is achieved can look different between working groups: it may require, for example: majority vote, `lazy consensus`_, or unanimous consent. The set of community members who are considered when evaluating consensus can also vary between working groups: it may involve everyone who attends meetings, everyone who contributes to group work, specific roles within the working group, or some other criteria.

What is important is that the group strives to reach consensus in a manner that is perceived as consistent and fair by those interested. In the circumstance that consensus cannot be sufficiently achieved in a working group, the group members may seek advisory from the group of `Core Contributors`_ or, in more challenging cases, the `Technical Oversight Committee`_.

Name and Purpose
----------------

Evey working group must have a specific name and statement of pupose. The purpose can be brief: a sentance or a paragraph is sufficient. The name and purpose of can be amended at any time via consensus of working group members, although major changes should be announced to the community.

Following this guideline will help community members understand the role of each working group in developing the Open edX platform. It will help them evaluate whether they would like to become involved in any given group.

Openness and Inclusivity
------------------------

As drivers of an open source project, Open edX working groups must make their activities as accessible to the public as reasonably possible. To this end, working groups should:

* Maintain an up-to-date listing of their name, purpose, and communication channels.
* Publicize any and all meeting times, preferrably in UTC, with an open invite to Open edX community members.
* Collaborate using publicly-visible communication channels.
* Generate and review work in publicly-visible spaces.
* Record significant decisions where they can be easily viewed by current and future community members.
* Welcome the participation of all community members who make good-faith efforts to contribute to the group's purpose.

One specific exception to these guidelines is the `Security Working Group`_, which publicizes their working model, but uses invite-only meetings and channels in order to responsibly handle platform security issues.

As with any activity in the Open edX community, behavior in working groups that conflicts with the `Open edX Code of Conduct`_ should be swiftly by the means detailed in that policy.

Chairperson
-----------

A working group must select by consensus a chairperson (or, a set of co-chairpeople) who will serve as a reliable point-of-contact for the group. The chairperson should work to ensure that the group meets the guidelines listed above. Although chairpersons often take leadership in the group's activities and facilitate its meetings, this is not required.

Chairpersons cannot be selected without their own consent.

When a chairperson wishes to retire from their role, they should make a brief announcement detailing:

* when they plan on stepping down,
* any information that will help their successor transition into the role, and
* optionally: any person or people who they recommend as their successor.

The working group should aim to fill the role before the departing chairperson steps down. If they cannot, the group is encouraged to reach out the `Core Contributors`_ group for assisstance.

Structure and Operation
-----------------------

Beyond the guidelines above, working groups have complete autonomy in their internal structure and operating model.

What do working groups do?
==========================

Generally speaking, working groups can contribute to the Open edX project in any way. For example, they might:

* coordinate amongst its various members' projects;
* help design and implement platform features;
* create workshops or courses;
* write documentation and architectural decision records (ADRs);
* triage issues;
* assist community members; or
* simply discuss an aspect Open edX platform.

Certain working group may step up to fulfill a responsibility called for in an Open edX Propsal. For example, at the time of writing, the Product Working Group is leading the implemenation `the Core Product from OEP-57`_, the DevOps Working Group handles `the release process from OEP-10`_, and the Architecture Coordination Working Group facilitates `the architecture advisory process from OEP-56`_. Due to the fluid nature of working groups, the association between OEP-declared responsibilities and specific working group names is subject to change over time.

Furthermore, working groups can step up to jointly `maintain`_ components of the Open edX platform, based on consensus of its members and of the Maintainer Scrum of Scrums.

How are working groups formed and dissolved?
============================================

To form a working group, any community member can share an announcement describing:

* the new group's name,
* its purpose,
* how to get involved in the new group, and
* optionally: who they suggest as the initial chairperson.

If the announcement generates sufficient interest, then the working group is formed.

At any time, working groups may merge together or split apart into multiple working groups, based on the consensus of all involved.

Eventually, it may make sense to dissolve the working group for various reasons, such as:

* inactivity,
* redundancy with another working group,
* lack of membership, or
* resolution of its purpose.

At this point, consensus among the remaining working group members is sufficient to dissolve the group. If the working group was responsible for handling any community processes, the group of Core Contributors should be notified so that the project can adapt accordingly. If the working group maintained any project components, then the Maintainer Scrum of Scrums should be informed so that they can resolve the resulting maintainance gap.

If a working group is inactive and their last known chairperson cannot be reached, then it will be considered de facto "dissolved".

For any merging, splitting, or dissolution of working groups, the community should be informed via an announcment, and any relevant documentation should be updated in order to reflect the new catalog of working groups.

Rationale
*********

The rationale adds to the specification by describing the events or
requirements that led to the proposal, what influenced the design, and why
particular design decisions were made. The rationale could provide evidence
of consensus within the community and discuss important objections or
concerns raised during discussion. It could identify any related work,
for example, how the feature is supported in other systems.

Rejected Alternatives
*********************

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

Change History
**************

YYYY-MM-DD
==========

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
