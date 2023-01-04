
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

An Open edX working group (WG) is a voluntary association of Open edX community members formed around some aspect of the project. These groups are organically formed and dissolved by consensus amongst those interested. While they must adhere to a basic set of standards, working groups otherwise have autonomy to decide their own structure and operating procedures.

Even before this OEP was codified, working groups played a vital role in sustaining and advancing the Open edX platform. In this OEP we aim to document their successful practices for the benefit of current and future Open edX community members.

Background & Motivation
***********************

The first community-lead Open edX working groups were the *Transifex Working Group* and the *Build-Test-Release Working Group*. The former group took responsibility for creating and curating translations of strings on the Open edX platform; the latter took responsility for the platform's named releases. These groups were formed by community members who saw a need in the project and took the initiative to organize and solve that need.

Since then, several more working groups have organized themselves using a similar model. The groups operate autonomously, but have largely standardized around a set of best practices to foster an inclusive and productive environment. Many of the groups play important roles in the project, such as organizing community events, developing standard user interface components, and driving project strategy. As a whole, working groups provide a space for Open edX contributors from different organizations and geographic regions to collaborate and form working relationships.

In this document, we aim to capture the generally-applicable processes and best practices for working groups so that they can be more readily understood by newcomers to the Open edX project and more easily referenced by all community members. In order to avoid disrupting the already-successful working group system that exists today, this OEP mandates only a lightweight framework, one which mostly aligns with how the community already operates.

Existing Guidance
=================

In spite of the importance of working groups to the health of the Open edX project, little formal documentation exists to establish the working group system. The only official guidance so far is found in the `Technical Oversight Committee (TOC) Charter`_:

    **10. Working Groups**

    a. From time-to-time as needed the TOC may create (and wind down) working groups or task forces that will be responsible for recommending and delivering critical project work and reporting on progress and results to the TOC.

    b. The TOC will typically delegate tactical decision making to those who best understand the details, including Working Groups, Task Forces, Expert Groups, etc. Only significant changes in the status quo or decisions that are difficult to reverse should be escalated to the TOC for consideration

This guidance is helpful, but it only describes working groups as they can be created by the TOC. In practice, working groups have been created and dissolved organically by various community members. In this OEP, we aim to complement and expand upon the guidance in the TOC Charter.

Furthermore, there exists an `outdated *Working Group Guidelines and Definition* wiki page <https://openedx.atlassian.net/wiki/spaces/COMM/pages/1007911045/Working+Group+Guidelines+and+Definition>`_ that describes best practices for working groups and states that "creating a new WG requires participation and/or permission from someone in a leadership position in the Open edX community." In this OEP, we aim to adapt the best practices from this page, while dropping the "leadership permission" requirement.


Specification
*************

What is a working group?
========================

An *Open edX Working Group* is any voluntnary association of Open edX community members that follows the following guidelines:

Establishment of consensus
--------------------------

Decisions made by a working group should be made by group *consensus*. The way consensus is achieved can look different between working groups: it may require, for example: majority vote, `lazy consensus`_, or unanimous consent. The set of community members who are considered when evaluating consensus can also vary between working groups: it may involve everyone who attends meetings, everyone who contributes to group work, specific roles within the working group, or some other criteria.

What is important is that the group strives to reach consensus in a manner that is perceived as consistent and fair by those interested. In the circumstance that consensus cannot be sufficiently achieved in a working group, the group members may seek advisory from the group of `Core Contributors`_ or, in more challenging cases, the `Technical Oversight Committee`_.

Specific name and purpose
-------------------------

Every working group must have a specific name and statement of purpose, where the purpose can be the benefit of any aspect of the Open edX platform or community. The purpose can be brief: a sentence or a paragraph is sufficient. The name and purpose can be amended at any time via consensus of working group members, although major changes should be announced to the community.

Following this guideline will help community members understand the role of each working group in furthering the Open edX platform. It will help them evaluate whether they would like to become involved in any given group.

Reliable chairperson
--------------------

A working group must select by consensus a chairperson (or, a set of co-chairpeople) who should serve as a reliable point-of-contact for the group. The chairperson should work to ensure that the group follows the best practices specified in this document. Although chairpersons often take leadership in the group's activities and facilitate its meetings, this is not required. Chairpersons cannot be selected without their own consent.

When a chairperson wishes to retire from their role, they should make a brief announcement detailing:

* when they plan on stepping down,
* any information that will help their successor transition into the role, and
* optionally: any person or people who they recommend as their successor.

The working group should aim to fill the role before the departing chairperson steps down. If they cannot, the group is encouraged to reach out the `Core Contributors`_ group for assistance.

Community benefit of work
-------------------------

The efforts of working groups are to be done for the benefit of the Open edX project. To ensure this:

* Artifacts produced by a working group (code, documents, images, decision records, etc.) must be hosted on systems administered by the Open edX project. This ensures that they will remain accessible to the community, even if the group is dissolved or if individual members move on from the project.
* Any produced code must be compatible with a license presently used by the Open edX project. Contributors of that code must have signed the `individual Contributor License Agreement (CLA)`_ or be specifically included under their organization's CLA. This ensures that there is no legal barrier for the community to use the working group's code as it would any other part of the Open edX codebase.
* Any produced documentation must be licensed under a `Creative Commons share alike license (CC-BY-SA)`_.

Openness and inclusivity
------------------------

As drivers of an open source project, Open edX working groups must make their activities as accessible to the public as reasonably possible. To this end, working groups should:

* Welcome the participation of all community members who make good-faith, non-disruptive efforts to contribute to the group's purpose.
* Collaborate using publicly-visible communication channels and/or task trackers.
* Generate and review work artifacts (code, documents, images, decisions, etc.) in publicly-visible spaces.
* Publicize any and all meeting times in UTC, with an open invite to Open edX community members.
* Maintain an up-to-date home page under the `Working Groups space`_ on the Open edX wiki, including:

  * the group's name and purpose,
  * the current chairperson and other assigned roles,
  * any meeting times and video conferencing links,
  * links to any communication channels or task trackers,
  * links to any spaces containing work artifacts, and
  * any additional documentation on group structure and operations.

Working groups may exempt themselves from certain guidelines when they would compromise their ability to do work related to:

* undisclosed security issues, or
* unreleased marketing materials.

Such groups should otherwise follow the openness & inclusivity guidelines to their best effort.

Finally, as with any activity in the Open edX community, working groups are subject to the `Open edX Code of Conduct`_.


Structure and operation
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

Certain working groups may step up to fulfill a responsibility called for in an Open edX Proposal. For example, at the time of writing, the Product Working Group is leading the implementation `the Core Product from OEP-57`_, the DevOps Working Group handles `the release process from OEP-10`_, and the Architecture Coordination Working Group facilitates `the architecture advisory process from OEP-56`_. Due to the fluid nature of working groups, the association between OEP-declared responsibilities and specific working group names is subject to change over time.

Furthermore, working groups can step up to support maintenance of Open edX components, per the process detailed in `OEP-55 <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0055-proc-project-maintainers.html>`_.

How are working groups formed and dissolved?
============================================

Forming
-------

To form a working group, any community member can share an announcement describing:

* the new group's name,
* its purpose,
* how to get involved in the new group, and
* optionally: who they suggest as the initial chairperson.

If the announcement generates sufficient interest, then the working group is formed. If not already selected, their first order of business should be to select a chairperson. Once selected, the new chairperson should publicise the new group's details as described above.

Changing
--------

At any time, by consensus amongst involved groups, working groups may merge together, split apart into multiple working groups, select a new chairperson, change their structure or processes, and so on. Working groups are encouraged to announce any significant changes to the broader community.

Dissolution
-----------

Eventually, it may make sense to dissolve the working group for various reasons, such as inactivity, redundancy, lack of participation, or resolution of the group's purpose. At this point, consensus among the remaining working group members is sufficient to commence dissolution of the group. 

If the working group was responsible for handling any community processes, the remaining members are encouraged to seek new stewards for those responsibilities; if they cannot, they should notify the group of Open edX Core Contributors, who can assist in filling the roles or escalate further. If the working group was named maintainer of any project components, then the remaining members should follow the `maintainership transfer process <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0055/decisions/0002-maintainership-transfer-process.html>`_.

One of the remaining working group members should update the working group's home page to reflect the dissolution. They should announce the dissolution and any related responsibility transfers to the community.

If there seem to be no remaining members in working group and the last chairperson cannot be reached, then the group is *de facto* dissolved. Any Core Contributor is encouraged to take the above steps in lieu of the abandoned working group.

Rejected Alternatives
*********************

No Formal Guidelines
====================

Without an OEP, working groups would still continue operating as they are today, possibly without any issues. So, we could have forgone this OEP.

However, we believe that when critical processes left undocumented, it becomes harder for newcomers to aquaint themselves with a project. Furthermore, undocumented proccesses work because certain community members happen to remember them; as those community members retire or move on to other projects, their knowledge can be lost. Finally, this OEP does add certain new guidelines that we believe are important; for example, the process to step down as a chairperson or disssolve a group are new.

Stricter Guidelines
===================

We could define a stricter framework for working groups. For example, we could require that new working groups be approved by some set of Open edX leaders.

TODO: Add some more justification on why we didn't make a stricter framework.

Change History
**************

YYYY-MM-DD
==========

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
