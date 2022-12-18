
OEP-62: Working Groups
######################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-62 <oep-0062-proc-working-groups>`
   * - Title
     - Working Groups
   * - Last Modified
     - 2023-01-XX
   * - Authors
     - Kyle McCormick <kyle@tcril.org>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - Accepted
   * - Type
     - Process
   * - Created
     - 2022-12-16
   * - Review Period
     - 2023-01-XX - 2023-01-XX
   * - Resolution
     - `Pull Request <https://github.com/openedx/open-edx-proposals/pull/424>`_
   * - References
     - * `List of working groups <https://openedx.atlassian.net/wiki/spaces/COMM/pages/46793351/Open+edX+Working+Groups>`_
       * `Working group calendar <https://calendar.google.com/calendar/u/0?cid=Y192ODZzaHJuZWdzaHNxZ3A0Zmoyazk0dTdiY0Bncm91cC5jYWxlbmRhci5nb29nbGUuY29t>`_

Abstract
********

Working groups are voluntary associations of Open edX community members formed around some aspect of the project. These groups are organically formed and dissolved by consensus amongst those interested. While they must follow a few basic community guidelines, working groups have autonomy to decide their own structure and operating procedures.

Even before this OEP was codified, working groups played a vital role in sustaining and advancing the Open edX platform. In this OEP we aim to document their successful practices for the benefit of current and future Open edX community members.

Background & Motivation
***********************

The first community-lead Open edX working groups were the *Transifex Working Group* and the *Build-Test-Release Working Group*. The former group took responsibility for creating and curating translations of strings on the Open edX platform; the latter took responsility for the platform's named releases. These groups were formed by community members who saw a need in the project and took the initiative to organize and solve that need.

Since then, several more working groups have organized themselves using a similar model. The groups operate autonomously, but have largely standardized around a set of best practices to foster an inclusive and productive environment. Many of the groups play important roles in the project, such as organizing community events, developing standard user interface components, and driving project strategy. As a whole, working groups provide a space for Open edX contributors from different organizations and geographic regions to collaborate and form working relationships.

Given the importance of working groups to the health of the Open edX project, it may surprise new Open edX contributors that litle formal documentation exists to establish the working group system. The only official guidance so far is found in the `Technical Oversight Committee (TOC) Charter`_:

    **10. Working Groups**

    a. From time-to-time as needed the TOC may create (and wind down) working groups or task forces that will be responsible for recommending and delivering critical project work and reporting on progress and results to the TOC.

    b. The TOC will typically delegate tactical decision making to those who best understand the details, including Working Groups, Task Forces, Expert Groups, etc. Only significant changes in the status quo or decisions that are difficult to reverse should be escalated to the TOC for consideration

While this excert is true, TOC-created working groups are only a special case of the broader working group system. In this document, we aim to capture the generally-applicable processes and best practices for working groups so that they can be more readily understood by newcomers to the Open edX project and more easily referenced by all community members. In order to avoid disruptive the already-successful working group system that exists today, this OEP mandates only a lightweight framework, one which mostly aligns with how the community already operates.


Specification
*************

What is a working group?
========================

An *Open edX Working Group* is any voluntnary association of Open edX community members that follows the following guidelines:

Consensus
---------

Decisions made by a working group should be made by group *consensus*. The way consensus is achieved can look different between working groups: it may require, for example: majority vote, `lazy consensus`_, or unanimous consent. The set of community members who are considered when evaluating consensus can also vary between working groups: it may involve everyone who attends meetings, everyone who contributes to group work, specific roles within the working group, or some other criteria.

What is important is that the group strives to reach consensus in a manner that is perceived as consistent and fair by those interested. In the circumstance that consensus cannot be sufficiently achieved in a working group, the group members may seek advisory from the group of `Core Contributors`_ or, in more challenging cases, the `Technical Oversight Committee`_.

Name and Purpose
----------------

Evey working group must have a specific name and statement of pupose. The purpose can be brief: a sentance or a paragraph is sufficient. The name and purpose of can be amended at any time via consensus of working group members, although major changes should be announced to the community.

Following this guideline will help community members understand the role of each working group in developing the Open edX platform. It will help them evaluate whether they would like to become involved in any given group.

Chairperson
-----------

A working group must select by consensus a chairperson (or, a set of co-chairpeople) who should serve as a reliable point-of-contact for the group. The chairperson should work to ensure that the group follows the best practices specified in this document. Although chairpersons often take leadership in the group's activities and facilitate its meetings, this is not required. Chairpersons cannot be selected without their own consent.

When a chairperson wishes to retire from their role, they should make a brief announcement detailing:

* when they plan on stepping down,
* any information that will help their successor transition into the role, and
* optionally: any person or people who they recommend as their successor.

The working group should aim to fill the role before the departing chairperson steps down. If they cannot, the group is encouraged to reach out the `Core Contributors`_ group for assisstance.

Openness and Inclusivity
------------------------

As drivers of an open source project, Open edX working groups must make their activities as accessible to the public as reasonably possible. To this end, working groups should:

* Welcome the participation of all community members who make good-faith, non-disruptive efforts to contribute to the group's purpose.
* Collaborate using publicly-visible communication channels and/or task trackers.
* Generate and review work artifacts (code, documents, images, decisions, etc.) in publicly-visible spaces.
* Publicize any and all meeting times in UTC, with an open invite to Open edX community members.
* Maintain an up-to-date listing of group metadata, including:

  * **name**,
  * **purpose**,
  * **chairperson**,
  * link(s) to **communication channels** and/or **task trackers**.
  * link(s) to any spaces containing **work artifacts**, and
  * link(s) to any additional documentation on group **structure and processes**.

Working groups may exempt themselves from certain guidelines when they would compromise their ability to do work related to:

* undisclosed security issues, or
* unreleased marketing materials.

Such groups should otherwise follow the openness & inclusivity guidelines to their best effort.

Finally, as with any activity in the Open edX community, behavior in working groups that conflicts with the `Open edX Code of Conduct`_ should be swiftly by the means detailed in that policy.


Structure and Operation
-----------------------

Beyond the guidelines above, working groups have complete autonomy in their internal structure and operating model. For example:

* they may define specific membership criteria, or deem membership to be fluid;
* they may define and assign additional roles, or choose to only have a chairperson;
* they may break themselves into teams or operate as a single unit;
* and so on.

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

Furthermore, working groups can step up to support maintenance Open edX components, per the process detailed in `OEP-55 <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0055-proc-project-maintainers.html>`_.

How are working groups formed and dissolved?
============================================

Forming
-------

To form a working group, any community member can share an announcement describing:

* the new group's name,
* its purpose,
* how to get involved in the new group, and
* optionally: who they suggest as the initial chairperson.

If the announcement generates sufficient interest, then the working group is formed. Their first order of business should be to select a chairperson. Once selected, new chairperson should publicly list the group's details as described above.

Changing
--------

At any time, by consensus amongst involved groups, working groups may merge together, split apart into multiple working groups, select a new chairperson, change their structure or processes, and so on. Working groups are encouraged to announce any significant changes to the broader community.

Dissolution
-----------

Eventually, it may make sense to dissolve the working group for various reasons, such as inactivity, redundancy, lack of participation, or resolution of the group's purpose. At this point, consensus among the remaining working group members is sufficient to commence dissolution of the group. 

If the working group was responsible for handling any community processes, the remaining members are encouraged to seek new stewards for those responsibilities; if they cannot, they should notify the group of Open edX Core Contributors, who can assist in filling the roles or escalate further. If the working group was named maintainer of any project components, then the remaining members should follow the `maintainership transfer process <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0055/decisions/0002-maintainership-transfer-process.html>`_.

Finally, one of the remaining working group members should update the public listing working groups and announce the dissolution and any related responsibility transfers to the community.

If there seem to be no remaining members in working group and the last chairperson cannot be reached, then the group is *de facto* dissolved. Any Core Contributor is enocuraged to take the above steps in lieu of the abandoned working group.

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
