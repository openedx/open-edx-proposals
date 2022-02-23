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

This document proposes that the Open edX community employ a scalable, structured, conversation-based approach to making architectural decisions, based around Architectural Decision Records (ADRs) and the Architectural Advisory Process, a process by which ADR authors gather input on their decisions.

It also proposes a "Decision Maker's Checklist" in support of the Advisory Process, and a format for a Technical Strategic Review as high-level input into our architectural direction and priorities, as a companion to business-focused planning ceremonies.

Motivation
==========

The proposals in this document are in response to a number of observations born out of conversations with engineering leaders in the Open edX community and 2U's OCM organization (formerly known as edX).  It's also strongly influenced by a pair of articles, `Scaling the Practice of Architecture, Conversationally <https://martinfowler.com/articles/scaling-architecture-conversationally.html>`_ and `Would you like architects with your architecture? <https://architectelevator.com/architecture/organizing-architecture>`_ which promote a decentralized vision of architectural process where decision making power is gathered in the hands of the engineers writing the code.

The realities described below, in the following sections, have ultimately built a culture of reactionary architecture - rather than intentional architecture - which we hope to improve.

Under-utilized ADRs
-------------------

We adopted ADRs in `OEP-19: Developer Documentation <https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0019-bp-developer-documentation.html>`_ several years ago as way of recording techincal decisions and co-locating them with their corresponding code.  OEP-19 is otherwise unopinionated about how those ADRs get used and reviewed, or when in our engineering process writing them should occur.  This has led to ambiguity and ambivalence around ADRs. Often they're an afterthought, and serve only as grudging documentation of a decision after it has been made.

We believe that ADRs - and more importantly, the conversations that happen around them - can be so much more.  We can use these documents and conversations as a vehicle for decentralizing and scaling our decision making process, making more informed decisions, democratizing access to domain knowledge, elevating our architectural discourse, and recording our decisions for posterity with additional contextual information that's often missing today.

Finding expertise is difficult
------------------------------

Related to the above, historically it is also very difficult to find experts and stakeholders in a given domain.  Generally this happens via word of mouth, sometimes not at all, or occasionally by engineers realizing a vacuum of expertise exists and *they are now the expert!*

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

- Formalizing and clarifying an **Architectural Advisory Process** in which we conversationally provide input on and promptly review the status of ADRs, as well as review updates to our architectural principles and tech radar, with the goal of enabling more effective, informed decision making.
- Improve the discoverability of stakeholders and experts via a **decision maker's checklist**.
- Documenting as a *suggested best practice* a quarterly **technical strategic review process** where we can review our technical strategy and cross-functional requirements as high-level, guiding input to the Advisory Process, and to help teams and organizations decide where to prioritize addressing "important but not urgent" work such as architectural investments, new capabilities, addressing technical debt, and performing ongoing maintenance/upgrades.

Architectural Advisory Process
------------------------------

We'll create an Architecture Advisory Process to add structure to today's informal review of ADRs.  The goal of this process is to review ADRs with relevant stakeholders and experts.  The process conversation needs to take place promptly so that decisions do not feel blocked on gathering advice.

We intend to also use the Advisory Process over time to revisit and iterate on our architectural principles with the goal of ensuring they're specific, measurable, achievable, realistic, and testable. (a variation of SMART criteria)  Similarly, like ADRs and architectural principles, we can use the process to review and adjust Open edX Tech Radar blips.

See `Appendix A: Advisory Process Principles <#Appendix A: Advisory Process Principles>`__  for more details on the spirit and functioning of the Advisory Process.

Advisory Process Artifacts
^^^^^^^^^^^^^^^^^^^^^^^^^^

The final artifact of the Advisory Process is an Architectural Decision Record document checked in to a repository relevant to the decision.  Prior to that, however, we suggest as a best practice using Github's Issues and Pull Request tooling to conduct the Advisory Process.  The advantage of doing so is that Issue/Pull Request comments remove the burden of recording who gave particular advice, and allow us to have threaded, focused conversations about various aspects of ADR.

Note that this section is GitHub-specific because that's where the Open edX platform's code resides.  The idea of "issues" and "pull requests" is otherwise portable to other version control platforms.

GitHub Issues (Optional)
************************

We would encourage decision makers to use a GitHub Issue to have initial conversations around the context of a decision, or to write down upcoming/potential decisions. It may be that - at the start of a project - we're not even sure what decisions need to be made.  They may not even reveal themselves until we're in the middle of coding.

An Issue is a great place to have these initial, loose, conversations about a decision space without the burden of creating a full ADR (creating a new document in the right place, authoring the sections of an ADR, creating a branch, committing, creating a PR, etc.)  It may not be necessary for more well-defined or focused decisions.  We want to preserve this as an optional first step, as we recognize that creating a "formal" ADR can feel inexplicably daunting or like a distraction, especially when possible decisions come up in the course of working on an implementation.  The mental burden of breaking "flow state" and creating a new ADR document is often problematic.  We hope jotting some notes in an Issue as a starting point will allow engineers to record their potential decision point and quickly continue their work.

GitHub Pull Requests
********************

Whether or not a GitHub Issue is used, a GitHub Pull Request will exist for creation/merging of the ADR.  This is the primary medium for the Advisory Process, and where we expect conversations will take place.  By the time a pull request is created, we expect the decision maker will have written down the context of the decision - necessary to orient advisors to the problem space - as well as draft of their potential decision.

Decision Maker's Checklist
--------------------------

In order to help ADR authors ensure they get advice from the proper set of stakeholders, we will create a checklist of possible groups/individuals to choose from.  It's the responsibility of the author to ensure the relevant people are aware of the ADR and are given the opportunity to provide feedback.

The Decision Maker's Checklist is a document that a decision authors can review to ensure they're seeking advice from the proper stakeholders and experts when authoring an ADR. It's that individual's responsibility to select all those stakeholders and experts (which may be individuals or groups) that are impacted by their decision based on their understanding of the scope.  Other individuals or groups may be added later as the scope and impact is better understood.

GitHub Groups
^^^^^^^^^^^^^

As a companion to the Decision Maker's Checklist, a set of GitHub groups will be created in the ``openedx`` organization that can be tagged on Pull Requests for ADRs.  The Decision Maker's Checklist will indicate the GitHub handles of individuals, or of these groups for use in creating pull requests.

Checklist Organization
^^^^^^^^^^^^^^^^^^^^^^

The Decision Maker's Checklist is a reference, and so decision maker's will be able to look up individuals by various criteria.  To this end, the checklist will list stakeholders and experts for products, domains, and by role, at a minimum.  This means that individuals or groups may appear more than once if they're experts on several different domains, or have multiple roles, for instance.

We will necessarily iterate on the organization of the checklist as we learn more about how we use it.

Technical Strategic Review (WIP)
--------------------------------

Coinciding with an organization's broader product strategy and prioritization cadence, establish quarterly technical strategic reviews focused on engineering and architectural priorities as they relate to our businesses' and platform's needs.  Depending on the size and team topologies of a given organization, The number of such meetings may vary.

There may several reviews, and they may be domain/theme-specific depending on the organization.  This process is not prescriptive on the number or cadence of review ceremonies, except to say that they should happen as frequently as an organization's business/product planning process occurs.

The review should be attended by engineering managers and at least one technical lead from each team in the group.

Strategic Review Artifacts (WIP)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Can we create a consistent set of artifacts to come out of technical strategic review that can be shared as input into downstream discussions (roadmap discussions, *other* strategic reviews, etc.)?  What should that artifact look like?

Proposal: A user story roadmap, such as: `Architecture User Story Roadmap <https://openedx.atlassian.net/wiki/spaces/AC/pages/1812037671/FY21-22+Architecture+User+Story+Roadmap>`_ from FY21-22.


Other Options Considered
========================

Require Engineers at Product Ceremonies
---------------------------------------

What if we just invite engineers to product planning ceremonies so they can get involved earlier?

Simply put, these are the wrong forums for the discussions engineers need to have.  Certainly some technical details are brought up, but they're not the focus, and the meeting doesn't have the right attendance to get into the details.

A review of notes from a recent product strategy meeting at 2U lends some credibility to this sentiment - the focus is on business-focused strategies and plans, not engineering-focused initiatives to support them.

Adding engineering ceremonies to prioritize a backlog of "important" work
-------------------------------------------------------------------------

We lack engineering ceremonies around identifying "important but not urgent" work: architectural investments, addressing technical debt, or performing ongoing maintenance.

The idea was to maintain a central backlog of "important" work that could then be handed out to teams as part of an up-front prioritization in parallel with the business' plans and strategies.

There are several problems with this:

- One is that the backlog was envisioned as centralized.  We believe that architectural choices - including whether or not to do work! - are best decided by those doing the work.  Not by some central authority.

- This approach - as envisioned - prioritizes output over outcomes.  Output is the fact that we updated the version of a certain dependency, or added a new capability.  Outcomes state *why* that was important to do, and are born out of our business' objectives and goals.

- Feedback on this indicated that to some extent this "important but not urgent" work was happening in many teams, in that they're able to advocate with their engineering leaders and product owners where necessary to ensure it gets prioritized.

Simply put, this feels like solving the wrong problem in a way that does nothing to empower our teams.

Synchronous Architecture Advisory Forum meeting
-----------------------------------------------

The approach to the Advisory Process advocated for in `Scaling the Practice of Architecture, Conversationally <https://martinfowler.com/articles/scaling-architecture-conversationally.html>`_ centers around the "Architecture Advisory Forum", a synchronous meeting where the group reviews ADRs, Architectural Principles, Tech Radar blips, etc.

We see three significant problems with the idea of having such a forum:

- Our community is global.  Any time we could propose for this meeting would necessarily leave out a significant portion of our stakeholders and experts.

- Our community is multi-faceted.  ADRs may have completely divergent sets of stakeholders.  How do we manage the guest list of such a meeting to ensure that the right voices are speaking on any given ADR, and that the right individuals are in the room?  It's a logistical nightmare given the size and scope of our community.

- Our community is comprised of many independent organizations.  There are times when our component organizations may want to use the Advisory Process on "internal" decisions as well.  Do we create a separate forum meeting for those decisions?  How do we then ensure that we err on the side of having an open, public forum?  Again, logistically, this is quite difficult.

Appendix A: Advisory Process Principles
=======================================

The creation of the Advisory Process is a fundamental shift in expectations of how we conduct our architectural process, and that shift requires an investment of time in order to be successful. The principles below describe the expectations, spirit and benefits of this process.

Asynchronous First
------------------

Our community is global, and so we will prioritize asynchronous communication in the Architecture Advisory Process. This means it's important that individuals be able to participate in the process even if they cannot attend a synchronous meeting.  We believe there's value in getting together synchronously, but the process should prioritize getting the right input over having synchronous meetings.  That said, if the relevant stakeholders and experts are able to schedule an effective in person meeting, there's additional value in getting together synchronously when possible.  Note, however, that the decision makers *still have the responsibility of recording in their ADR the advice they received.*

Decisions must document the advice they receive
-----------------------------------------------

One of the core tenets of the Advisory Process is that it is the decision maker's responsibility to record the advice they received, whether or not they chose to take it, and why.  We recognize that this is a time investment, but believe it will lead to better decisions, better documentation, and an approachable process where stakeholders and experts feel welcomed and encouraged to provide their invaluable insights.

Decentralized decision making
-----------------------------

We assert that anyone can make an architectural decision, and that the only caveat is that they must consult those who are meaningfully affected by the decision, and those who have expertise in the area the decision is being taken.  The decision and all the advice they receive should be documented in an ADR, co-located with the relevant code following our established ADR guidelines in OEP-19.

Encourage approachable decision making
--------------------------------------

The process of making a technical decision is not atomic, nor do many parts of it occur while writing a document about the decision.  Many decisions are *made* while writing code as we problem solve and navigate the idiosyncracies of the system.  The goal of our process should be to find a way of sharing and capturing those decisions where they impact others.  In this spirit, we encourage using "low-friction" artifacts - such as GitHub Issues - in those moments where drafting a full ADR document would be prohibitively distracting.  An issue is an easy place to gather quick, initial input, and naturally becomes resolved by a GitHub Pull Request - the ADR - which describes the decision in more detail.

Decisions can have varying impacts and scopes
---------------------------------------------

The group of impacted individuals for a given decision may vary wildly, and some decisions will necessarily require more input than others.  A developer iterating on new "green field" code may not need to consult *anyone* except their immediate teammates, whereas a team making changes to a core data model may impact stakeholders and systems throughout the platform, meaning that there will necessarily be significant input into the decision.  The volume of input may make the latter decision harder to make.  That the complexity of the decision making process correlates with the impact of the decision indicates we're giving decisions due process.

Decisions are an opportunity to learn and democratize knowledge
---------------------------------------------------------------

Engaging in public conversations around our decision making invites participation and creates learning opportunities.  An informed developer community who feels empowered to engage in the decision making process has numerous downstream benefits.  It deepens our well of expertise, increases retention for participating companies, provides opportunities for mentorship and upskilling, and ensures the process receives relevant and diverse input.
