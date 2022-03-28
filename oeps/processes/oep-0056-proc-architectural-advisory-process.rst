======================================
OEP-56: Architectural Advisory Process
======================================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-56 <oep-0056-proc-architectural-advisory-process>`
   * - Title
     - Architectural Advisory Process
   * - Last Modified
     - 2022-02-25
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
     - 2022-02-28 - 2022-03-28

.. contents::
   :local:
   :depth: 3

Abstract
========

This document proposes that the Open edX community employ a scalable, structured, `conversation-based <conversational principle_>`_ approach to making architectural decisions.  This approach is based around Architectural Decision Records (ADRs) and the Architectural Advisory Process, a lightweight, asynchronous process by which decision makers gather input on their decisions.  It also proposes a "Decision Impact Checklist" in support of the Advisory Process.

Quick Process Summary
=====================

- Anyone can use an architectural decision record (ADR) as a tool to make a decision.
- Decision makers use a "Decision Impact Checklist" of stakeholders and subject matter experts to understand who is impacted by their decision, and who has relevant expertise.
- They ask all those people to review their decision Pull Request.
- Reviewers give their advice in a "conversational" way - they are input into the process, they're not the decider.
- The decision maker's responsibility is to write down all the advice they receive, and why and whether or not they chose to take it.
- The decision maker then makes their decision based on this input and merges the Pull Request.

Motivation
==========

The proposals in this document are in response to a number of observations born out of conversations with engineering leaders in the Open edX community and 2U's OCM organization (formerly known as edX).  It's also strongly influenced by a pair of articles, `Scaling the Practice of Architecture, Conversationally <conversationally_>`_ and `Would you like architects with your architecture? <architects_>`_ which promote a decentralized vision of architectural process where decision making power is gathered in the hands of the engineers writing the code.

The realities described below, in the following sections, have ultimately built a culture of reactionary architecture - rather than intentional architecture - which we hope to improve.

Under-utilized ADRs
-------------------

We adopted ADRs in :doc:`../best-practices/oep-0019-bp-developer-documentation` several years ago as way of recording technical decisions and co-locating them with their corresponding code.  OEP-19 is otherwise unopinionated about how those ADRs get used and reviewed, or when in our engineering process writing them should occur.  At times this can lead to ambiguity and ambivalence around ADRs; sometimes they're created as an afterthought and serve only as documentation of a decision after it has been made.

We believe that ADRs - and more importantly, the conversations that happen around them - can be so much more.  We can use these documents and conversations as a vehicle for decentralizing and scaling our decision making process, making more informed decisions, democratizing access to domain knowledge, elevating our architectural discourse, and recording our decisions for posterity with additional contextual information that's often missing today.

Finding expertise is difficult
------------------------------

Related to the above, historically it is also very difficult to find subject matter experts and stakeholders in a given domain.  Generally this happens via word of mouth, sometimes not at all, or occasionally by engineers realizing a vacuum of expertise exists and *they are now the expert!*

This difficulty is compounded by the decentralized and global nature of the Open edX community.

Atrophy of architectural principles and tech radar
--------------------------------------------------

An important component of our ability to make architectural decisions that are aligned across teams and organizations is measuring and vetting those decisions against a set of `shared architectural principles`_ and technologies in our `Tech Radar`_.  Today, we lack organized maintenance of our principles and radar, nor do we have an appropriate forum in which to discuss and socialize them.

Specification
=============

We believe that we can create a healthy and empowering dialogue around our architectural process and mitigate the above concerns by clarifying the spirit and process around the creation and review of ADRs.

We will adjust our current architectural decision making process by:

- Formalizing and clarifying an **Architectural Advisory Process** in which we conversationally provide input on and promptly review the status of ADRs, as well as review updates to our architectural principles and tech radar, with the goal of enabling more effective, informed decision making.
- Improve the discoverability of impacted stakeholders and subject matter experts via a **decision impact checklist**.
- Set decision makers up for success by reducing the barrier to entry for new ADRs.  This may include an updated ADR template, PR templates, a short step by step guide, and increasing the visiblity and discoverability of active decisions and documentation on the AAP.
- Maintain cross-team and cross-organization alignment by using the Architectural Advisory Process to **vet changes to our architectural principles and tech radar**.  Our architectural values and technology choices are both are crucial inputs into the decision making process.

Architectural Advisory Process (AAP)
------------------------------------

We'll create an Architecture Advisory Process to add structure to today's informal review of ADRs.  The goal of this process is to review ADRs with relevant stakeholders and subject matter experts.  Conversations in the advisory process need to take place promptly so that decisions do not feel blocked on gathering advice.

We intend to also use the AAP over time to revisit and iterate on our architectural principles with the goal of ensuring they're specific, measurable, achievable, realistic, and testable (a variation of SMART criteria).  Similarly, like ADRs and architectural principles, we can use the process to review and adjust Open edX Tech Radar blips.

This process is intended to be lightweight, approachable, and asynchronous.  This section dives what that means, as well as the spirit of the process.

Advisory Process Principles
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The creation of the Advisory Process is a fundamental shift in expectations of how we conduct our architectural process, and that shift requires an investment of time in order to be successful. The principles below describe the expectations, spirit and benefits of this process.

Asynchronous First
******************

Our community is global, and so we will prioritize asynchronous communication in the Architecture Advisory Process. This means it's important that individuals be able to participate in the process even if they cannot attend a synchronous meeting.  We believe there's value in getting together synchronously, but the process should prioritize getting the right input over having synchronous meetings.  That said, if the relevant stakeholders and subject matter experts are able to schedule an effective in person meeting, there's additional value in getting together synchronously when possible.  Note, however, that the decision makers *still have the responsibility of recording in their ADR the advice they received.*

.. _conversational principle:

Stakeholders and subject matter experts give advice, conversationally
*********************************************************************

Impacted individuals participate in the process to share what they know and their opinions on how to make the decision.  The spirit of this is a conversation in which they give the decision maker advice and input.  Their goal is to empower the decision maker, not to control the process.

See `Scaling the Practice of Architecture, Conversationally <conversationally_>`_ for more information on the usage of "conversationally" in this context.

.. _document advice:

Decisions must document the advice they receive
***********************************************

One of the core tenets of the Advisory Process is that it is the decision maker's responsibility to record the advice they received, whether or not they chose to take it, and why.  We recognize that this is a time investment, but believe it will lead to better decisions, better documentation, and an approachable process where stakeholders and subject matter experts feel welcomed and encouraged to provide their invaluable insights.

Responsibility of decentralized decision making
***********************************************

We assert that anyone can make an architectural decision, and that the only caveat is that they **must consult those who are meaningfully affected by the decision**, and those who have expertise in the area the decision is being taken.  The decision and all the advice they receive should be documented in an ADR, co-located with the relevant code following our established ADR guidelines in OEP-19.

Encourage approachable decision making
**************************************

The process of making a technical decision is not atomic, nor do many parts of it occur while writing a document about the decision.  Many decisions are *made* while writing code as we problem solve and navigate the idiosyncracies of the system.  The goal of our process should be to find a way of sharing and capturing those decisions where they impact others.  In this spirit, we encourage using "low-friction" artifacts - such as GitHub Issues - in those moments where drafting a full ADR document would be prohibitively distracting.  An issue is an easy place to gather quick, initial input, and naturally becomes resolved by a GitHub Pull Request - the ADR - which describes the decision in more detail.

Decisions can have varying impacts and scopes
*********************************************

The group of impacted individuals for a given decision may vary wildly, and some decisions will necessarily require more input than others.  A developer iterating on new "green field" code may not need to consult *anyone* except their immediate teammates, whereas a team making changes to a core data model may impact stakeholders and systems throughout the platform, meaning that there will necessarily be significant input into the decision.

The volume of input may make the latter decision harder to make.  That the complexity of the decision making process correlates with the impact of the decision indicates we're giving decisions due process.  An overly complex decision with too many stakeholders may also be an indication that it should be broken down into more manageable pieces.

Decisions are an opportunity to learn and democratize knowledge
***************************************************************

Engaging in public conversations around our decision making invites participation and creates learning opportunities.  An informed developer community who feels empowered to engage in the decision making process has numerous downstream benefits.  It deepens our well of expertise, increases retention for participating companies, provides opportunities for mentorship and upskilling, and ensures the process receives relevant and diverse input.

This is an *architectural* decision making process
**************************************************

This process is for architectural decisions.  It's not for product, user experience, pedagogical, legal, etc., decisions that are handled by other, non-engineering groups in our community.

Advisory Process Artifacts
^^^^^^^^^^^^^^^^^^^^^^^^^^

The final artifact of the Advisory Process is an Architectural Decision Record document checked in to a repository relevant to the decision.  Prior to that, however, we suggest as a best practice using Github's Issues and Pull Request tooling to conduct the Advisory Process.  The advantage of doing so is that Issue/Pull Request comments remove the burden of recording who gave particular advice, and allow us to have threaded, focused conversations about various aspects of ADR.

Note that this section is GitHub-specific because that's where the Open edX platform's code resides.  The idea of "issues" and "pull requests" is otherwise portable to other version control platforms.

GitHub Issues (Optional)
************************

We would encourage decision makers to use a GitHub Issue to have initial conversations around the context of a decision, or to write down upcoming/potential decisions. It may be that - at the start of a project - we're not even sure what decisions need to be made.  They may not even reveal themselves until we're in the middle of coding.

An Issue is a great place to have these initial, loose conversations about a decision space without the burden of creating a full ADR. We want to preserve this as an optional first step, as we recognize that creating a "formal" ADR can feel daunting or like a distraction. We hope jotting some notes in an Issue as a starting point will allow engineers to record their potential decision point and quickly continue their work.

Discourse (Optional)
********************

Some decisions benefit from a wide broadcast for input.  In such cases, it may be helpful to use established channels - like Open edX's Discourse Forums - in order to have a public conversation on the topic to gather early input.  Note that
like a GitHub Issue, we want to ensure that this part of the conversation is preserved, so it should ultimately be linked from the GitHub PR for the resulting ADR if not the ADR itself.  Ideally the received advice will be distilled and recorded in the ADR itself, as per `Decisions must document the advice they receive <document advice_>`_

GitHub Pull Requests
********************

Whether or not a GitHub Issue is used, a GitHub Pull Request will exist for creation/merging of the ADR.  This is the primary medium for the Advisory Process, and where we expect conversations will take place.  By the time a pull request is created, we expect the decision maker will have written down the context of the decision - necessary to orient advisors to the problem space - as well as draft of their potential decision.

Decision Impact Checklist
-------------------------

In order to help decision makers ensure they get advice from impacted stakeholders and relevant subject matter experts, we will create a checklist of possible groups/individuals to choose from.  It's the responsibility of the author to select all those stakeholders and experts (which may be individuals or groups) that are impacted by their decision based on their understanding of the scope.  Other individuals or groups may be added later as the scope and impact is better understood.

GitHub Teams
^^^^^^^^^^^^

As a companion to the checklist, a set of GitHub teams will be created in the ``openedx`` organization that can be tagged on Pull Requests for ADRs.  The Decision Impact Checklist will indicate the GitHub handles of individuals, or of these teams for use in creating pull requests.

Checklist Format
^^^^^^^^^^^^^^^^

The checklist is a reference, and so decision makers will be able to look up individuals by various criteria.  To this end, the checklist will list stakeholders and subject matter experts for products, domains, and by role, at a minimum.  This means that individuals or groups may appear more than once if they're experts on several different domains, or have multiple roles, for instance.

We will necessarily iterate on the organization of the checklist as we learn more about how we use it.  This OEP also does not prescribe where the checklist needs to be written down.

Other Options Considered
========================

Synchronous Architecture Advisory Forum meeting
-----------------------------------------------

The approach to the Advisory Process advocated for in `Scaling the Practice of Architecture, Conversationally <conversationally_>`_ centers around the "Architecture Advisory Forum", a synchronous meeting where the group reviews ADRs, Architectural Principles, Tech Radar blips, etc.

We see three significant problems with the idea of having such a forum:

- Our community is global.  Any time we could propose for this meeting would necessarily leave out a significant portion of our stakeholders and subject matter experts.

- Our community is multi-faceted.  ADRs may have completely divergent sets of stakeholders.  How do we manage the guest list of such a meeting to ensure that the right voices are speaking on any given ADR, and that the right individuals are in the room?  It's a logistical nightmare given the size and scope of our community.

- Our community is comprised of many independent organizations.  There are times when our component organizations may want to use the Advisory Process on "internal" decisions as well.  Do we create a separate forum meeting for those decisions?  How do we then ensure that we err on the side of having an open, public forum?  Again, logistically, this is quite difficult.

.. _conversationally: https://martinfowler.com/articles/scaling-architecture-conversationally.html
.. _architects: https://architectelevator.com/architecture/organizing-architecture
.. _shared architectural principles: https://openedx.atlassian.net/wiki/spaces/AC/pages/921895082/Architecture+Vision+Principles
.. _Tech Radar: https://github.com/openedx/openedx-tech-radar

Change History
==============

2022-03-28

* Folded "Appendix A" back into the document, as per feedback.  It's an important part of the flow of the OEP, and extracting it in an appendix made the document less clear overall.
* Renamed "Decision Maker's Checklist" to "Decision Impact checklist" to be clearer around the role it plays in the process.  A separate "decision maker's checklist" may exist to help walk people through the overall AAP.
* Added some more cross-links in the document to (hopefully) help readers find information and understand concepts.
* Added a section about Discourse forums and the role they (and other mediums) might play in the AAP.

2022-03-23
----------

* Adding change history section and addressing PR feedback.
* Adding "proc" prefix to the filename.
* Removing usage of "ADR authors" and "decision authors" in favor of "decision makers"
* Clarifying "experts" to be "subject matter experts"
* `Pull request #305 <https://github.com/openedx/open-edx-proposals/pull/305>`_

2022-02-25
----------

* Ready for review.
* Removed sections on "Technical Strategic Review" in favor of focusing solely on the Architecture Advisory Process.  This focuses the OEP on tactical architectural decision making, rather than trying to also include a suggestion for how to handle architectural strategic planning.
* `Pull request #305 <https://github.com/openedx/open-edx-proposals/pull/305>`_

2022-02-09
----------

* Document created.
* `Pull request #305 <https://github.com/openedx/open-edx-proposals/pull/305>`_
