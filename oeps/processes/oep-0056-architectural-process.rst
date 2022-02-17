=============================
OEP-56: Architectural Process
=============================
.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-56 <oep-0056-architectural-process>`
   * - Title
     - Architectural Process
   * - Last Modified
     - 2022-02-09
   * - Authors
     - David Joy <djoy@edx.org>
   * - Arbiter
     - Feanil Patel <feanil@tcril.org>
   * - Status
     - Accepted
   * - Type
     - Process
   * - Created
     - 2022-02-09
   * - Review Period
     - 2022-02-09 - 2022-03-11

.. contents::
   :local:
   :depth: 3

Abstract
========

This document proposes that the Open edX community employ a scalable, structured, conversation-based approach to making architectural decisions, based around Architectural Decision Records (ADRs) and the Architectural Advisory Forum, a process by which ADR authors gather input on their decisions.

Motivation
==========

The proposals in this document are in response to a number of observations borne out of conversations with engineering leaders in the Open edX community and 2U's OCM organization (formerly known as edX).  It's also strongly influenced by a pair of articles, `Scaling the Practice of Architecture, Conversationally <https://martinfowler.com/articles/scaling-architecture-conversationally.html>`_ and `Would you like architects with your architecture? <https://architectelevator.com/architecture/organizing-architecture>`_ which promote a decentralized vision of architectural process where decision making power is gathered in the hands of the engineers writing the code.

The realities described in the following sections have ultimately built a culture of reactionary architecture - rather than intentional architecture - which we hope to improve.

Under-utilized ADRs
-------------------

We adopted ADRs in `OEP-19: Developer Documentation <https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0019-bp-developer-documentation.html>`_ several years ago as way of recording techincal decisions and co-locating them with their corresponding code.  OEP-19 is otherwise unopinionated about how those ADRs get used and reviewed, or when in our engineering process writing them should occur.  This has led to ambiguity and ambivalence around ADRs. Often they're an afterthought, and serve only as documentation of a decision after it has been made.

We believe that ADRs - and more importantly, the conversations that happen around them - can be so much more.  We can use these documents and conversations as a vehicle for decentralizing and scaling our decision making process, making more informed decisions, democratizing access to domain knowledge, elevating our architectural discourse, and recording our decisions for posterity with additional contextual information that's often missing today.

Finding expertise is difficult
------------------------------

Related to the above, historically it's also very difficult to find experts and stakeholders in a given domain.  Generally this happens via word of mouth, sometimes not at all, or occasionally by engineers realizing a vacuum of expertise exists and *they are now the expert!*

This difficulty is compounded by the decentralized and global nature of the Open edX community.

Atrophy of architectural principles and tech radar
--------------------------------------------------

An important component of our ability to make architectural decisions that are aligned across teams and organizations is measuring and vetting those decisions against a set of `shared architectural principles <https://openedx.atlassian.net/wiki/spaces/AC/pages/921895082/Architecture+Vision+Principles>`_ and technologies in our `Tech Radar <https://github.com/openedx/openedx-tech-radar>`_.  Today, we lack organized maintenance of our principles and radar, nor do we have an appropriate forum in which to discuss and socialize them.

Missing ceremonies
------------------

As a community of companies invested in the success of the Open edX platform, we lack any sort of regular ceremonies in which we discuss the platform's architectural priorities as they relate to our desired platform and organization-specific goals.  This often leads to a situation where architectural thinking is done primarily by senior engineering leaders as they learn about their business' needs, and by engineers in the course of projects, with little structured decision making in between to help bridge the highly strategic and the tactical.  Furthermore, the conversations that do happen tend to occur in an ad hoc way - often as "hallway conversations" or one-off project syncs - with whoever can be found to offer enough input to unblock the team.

Specification
=============

We believe that we can create a healthy and empowering dialogue around our architectural process and mitigate the above concerns by focusing on a re-alignment of ceremonies and clarifying the spirit and process around the creation and review of ADRs.

We will adjust our current architectural process by:

- Creating an architectural advisory process in which we conversationally provide input on and regularly review the status of ADRs, as well as review updates to our architectural principles and tech radar.
- Formalizing and clarifying when and how to use ADRs and the advisory process to make effective, informed decisions.
- Improve the discoverability of stakeholders and experts via a decision maker's checklist.
- Documenting as a *suggested best practice* a quarterly technical strategic review process where we can review our technical strategy and cross-functional requirements as high-level, guiding input to the advisory process, and to help teams and organizations decide where to prioritize addressing "important but not urgent" work such as architectural investments, addressing technical debt, and performing ongoing maintenance/upgrades.

Architectural Advisory Process
------------------------------

We'll create an Architecture Advisory Process to add structure to today's informal review of ADRs.  The goal of this process is to review ADRs with relevant stakeholders and experts.  The process conversation needs to take place promptly so that decisions do not feel blocked on gathering advice.

We intend to also use the advisory process over time to revisit and iterate on our architectural principles with the goal of ensuring they're specific, measurable, achievable, realistic, and testable. (a variation of SMART criteria)  Similarly, like ADRs and architectural principles, we can use the process to review and adjust Open edX Tech Radar blips.

Asynchronous First
^^^^^^^^^^^^^^^^^^

Our community is global, and so we will prioritize asynchronous communication in the Architecture Advisory Process. This means it's important that individuals be able to participate in the process even if they cannot attend a synchronous meeting.  We believe there's value in getting together synchronously, but the process should prioritize getting the right input over having synchronous meetings.  That said, if the relevant stakeholders and experts are able to schedule an effective in person meeting, there's additional value in getting together synchronously when possible.  Note, however, that the decision makers *still have the responsibility of recording in their ADR the advice they received.*

Advisory Process Artifacts
^^^^^^^^^^^^^^^^^^^^^^^^^^

The final artifact of the advisory process should be an Architectural Decision Record document checked in to a repository relevant to the decision.  Prior to that, however, we suggest as a best practice using Github's Issues and Pull Request tooling to conduct the advice process.  The advantage of doing so is that Issue/Pull Request comments remove the burden of recording who gave particular advice, and allow us to have threaded, focused conversations about various aspects of ADR.

(Optional) GitHub Issues
************************

We would encourage decision makers to use a GitHub Issue to have initial conversations around the context of a decision, or to write down upcoming/potential decisions. It may be that - at the start of a project - we're not even sure what decisions need to be made.  They may not even reveal themselves until we're in the middle of coding.

An Issue is a great place to have these initial, loose, conversations about a decision space without the burden of creating a full ADR (creating a new document in the right place, authoring the sections of an ADR, creating a branch, committing, creating a PR, etc.)  It may not be necessary for more well-defined or focused decisions.  We want to preserve this as an optional first step, as we recognize that creating a "formal" ADR can feel inexplicably daunting or like a distraction, especially when possible decisions come up in the course of working on an implementation.  The mental burden of breaking "flow state" and creating a new ADR document is often problematic.  We hope jotting some notes in an Issue as a starting point will allow engineers to record their potential decision point and quickly continue their work.

GitHub Pull Requests
********************

Whether or not a GitHub Issue is used, a GitHub Pull Request will exist for creation/merging of the ADR.  This is the primary medium for the Architectural Advisory Process, and where we expect conversations will take place.  By the time a pull request is created, we expect the decision maker will have written down the context of the decision - necessary to orient advisors to the problem space - as well as draft of their potential decision.

Advisory Forum Principles
^^^^^^^^^^^^^^^^^^^^^^^^^

The creation of the advisory forum is a fundamental shift in expectations of how we conduct our architectural process, and that shift requires an investment of time in order to be successful. The principles below describe both the expectations and the benefits of this process.

Decentralized decision making
*****************************

We assert that anyone can make an architectural decision, and that the only caveat is that they must consult those who are meaningfully affected by the decision, and those who have expertise in the area the decision is being taken.  The decision and all the advice they receive should be documented in an ADR, co-located with the relevant code following our established ADR guidelines in OEP-19.

Decisions can have varying impacts and scopes
*********************************************

The group of impacted individuals for a given decision may vary wildly, and some decisions will necessarily require more input than others.  A developer iterating on new "green field" code may not need to consult *anyone* except their immediate teammates, whereas a team making changes to a core data model may impact stakeholders and systems throughout the platform, meaning that there will necessarily be significant input into the decision.  The volume of input may make the latter decision harder to make - this is, in a sense, by design.

Decisions must document the advice they receive
***********************************************

For any scope of decision, the decision maker's responsibility is to record the advice they received, whether or not they chose to take it, and why.  This is, certainly, a time investment, but we believe it will both lead to better

Decisions are an opportunity to learn and democratize knowledge
***************************************************************

Assume decisions will be made
*****************************

As a rule of thumb, we should open up an ADR pull request in an appropriate repository when starting a new project.  This may not be the only ADR we create, but it should serve as a reminder and a place to take notes as we start to make decisions.

We want ADRs to be a central component of our architectural process.  It's alright for them to evolve (and perhaps split) as we discover the shape of an effort.


Approachable Decision Making
----------------------------

The process of making a technical decision is not atomic, nor do many parts of it occur while writing a document about the decision.  Many decisions are *made* while writing code as we problem solve and navigate the idiosyncracies of the system.  The goal of our process should be to find a way of capturing those decisions

Decision Maker's Checklist
--------------------------

In order to help ADR authors ensure they get advice from the proper set of stakeholders, we will create a checklist of possible groups/individuals to choose from.  It's the responsibility of the author to ensure the relevant people are aware of the ADR and are given the opportunity to provide feedback, either individually, in separate sessions, or in the advisory forum.

Technical Strategic Review
--------------------------

Coinciding with the broader product strategy and prioritization cadence, establish quarterly strategic reviews focused on engineering and architectural priorities as they relate to our business and platform needs.  There may several reviews, and they may be domain/theme-specific depending on the organization.

The review should be attended by engineering managers and at least one technical lead from each team in the



Review ADRs at the Architectural Advisory Forum
-----------------------------------------------


Organize quarterly engineering strategic review sessions
--------------------------------------------------------


Options Considered
==================

Decentralize Decision Making and Promote ADRs
---------------------------------------------

This is not the first option we arrived at, but feels as if it most directly addresses the scalability and empowerment issues we're seeing in our process.

Invite and Involve Senior Individual Contributors (ICs) to Product Rituals
--------------------------------------------------------------------------

What if we just invite engineers to Strategic Review and Consumer Review so they can get involved earlier?

Simply put, we don't believe that senior/principal ICs would be interested in much of what happens at Strategic Review and Consumer Review, as those conversations are centered around product/business concerns and expressly don't get into the weeds of engineering details.

A review of notes from Strategic Review (I didn't go!) lends some credibility to this sentiment - it was very difficult to translate from the "outside voice" used in the meeting to the "inside voice" employed by engineers when discussing a project.

New Engineering Rituals Coinciding with Product Prioritization
--------------------------------------------------------------

Our engineering team had no similar rituals in which to discuss the engineering/architectural implications of the strategy and plans vetted in Strategic Review and Consumer Review.

An early idea was to hold a similar, quarterly, organization-wide strategic engineering review, out of which we would have focused, follow-up breakout meetings with stakeholders for a particular initiative.

The benefits of this is that it allows us to have focused, up-front conversations about our engineering and architectural priorities, which generally include things like architectural investments, addressing technical debt, or taking on ongoing maintenance.

Feedback on this from Gabe Mulley, Mike Terry, and others indicated that to some extent this "important but not urgent" work was happening in many teams, in that they're able to advocate with their engineering leaders and product owners where necessary to ensure it gets prioritized.
