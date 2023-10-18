.. _pep_based_template:

OEP-63: TOC Resolution Request
##############################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0063 <oep-0063-proc-toc-resolution-request>`
   * - Title
     - TOC Resolution Request
   * - Last Modified
     - 2023-05-29
   * - Authors
     - Xavier Antoviaque
   * - Arbiter
     - Ed Zarecor
   * - Status
     - Draft
   * - Type
     - Process
   * - Created
     - 2023-05-18
   * - Review Period
     - <start - target end dates for review> -- until the next TOC meeting?
   * - References
     - `TOC meeting notes - governance norms <https://discuss.openedx.org/t/2023-04-11-toc-meeting-summary/10019#governance-norms-5>`_

Abstract
********

The Technical Oversight Committee (TOC) is meant to be a resource for the Open edX project. Thus it is important that there are clear lines of communication between the TOC and Open edX community. The purpose of this OEP is to establish and define those lines of communication.

The TOC aims to empower the project's contributors to make decisions. The person closest to the work will often know best, and empowered contributors face less friction. 

However, some of the topics can be complex, such as making changes which will have long-lasting repercussions or when there is no consensus on a topic within the community. In this OEP, the TOC defines a process for community members to bring up topics to the committee's attention, and request its support on the matter. The TOC responds through the adoption of resolutions, for example intervening on the discussion or taking a decision about a proposal. 

Motivation
**********

The TOC discussed during the April 2023 meeting a `series of guidelines for operating and making decisions <https://discuss.openedx.org/t/2023-04-11-toc-meeting-summary/10019#governance-norms-5>`_. This includes establishing a way for the community to request support from the TOC on specific topics and decisions, which this OEP implements.

Guidelines
==========

From the `TOC meeting notes <https://discuss.openedx.org/t/2023-04-11-toc-meeting-summary/10019#governance-norms-5>`_:

*The TOC should not operate “in the weeds.” We don’t have the time to be involved day-to-day technical decisions, and those closest to the work will make the best decisions. The TOC should not act as a “stage gate.” We should not create friction for contributors unnecessarily, but should:*
   
- *Guide the project toward shared goals*
- *Help refine and improve plans and designs*
- *Prevent the project from making bad decisions that are hard to reverse.*
   
*The TOC should weigh in:*
   
- *on “one-way-doors,” decisions that are hard to reverse, where choices are mutually exclusive*
- *when the community is having difficulty reaching consensus*
- *when an important decision is being deferred and not making a call is causing friction*
- *to focus the community to collaborate on key, shared value, say, investment in maturing LTI*

Specification
*************

- Resolution Requests for the TOC can be posted by anyone in the community - but need to be supported by at least two core contributors or a TOC member
- The TOC members are encouraged to participate in community discussions early on, before the TOC is asked to arbitrate.

Step-by-step - How to Submit a Resolution Request
=================================================

- Open a discussion in a public place, such as the `forum <discuss.openedx.org/>`_ or using one of the formal decision recording formats such as `OEPs <https://open-edx-proposals.readthedocs.io/en/latest/>`_. The topics presented need to have been discussed in the community before being formally considered by the TOC.
- After public review, post a formal Resolution Request to the TOC, by opening a `github ticket <https://github.com/openedx/wg-coordination/issues/new>`_. Mention the `TOC chair <https://openedx.atlassian.net/wiki/spaces/COMM/pages/3575939113/Technical+Oversight+Committee+TOC#Details>`_ in the request to make sure they get notified. See the format below.
- Notify the participants of the public discussions that you have created the TOC Resolution Request, by posting a link to it in the discussion threads.
- Core contributors and/or TOC members supporting the Resolution Request reply in the thread to say so.
- If the requirements are met, the TOC will schedule the topic for a future meeting. 
- If the Resolution Request contains a suggested decision, the TOC can choose to accept the suggested decision, with or without changes, or to refuse it. 
- The adopted Resolution is communicated as a reply to the github ticket, with a comment to explain it.

Resolution Request - Github Ticket Template
===========================================

.. code-block:: none

   Subj: TOC Resolution Request - [Title]
   
   ---------------------
   
   ## [Title]
   
   ## Summary 
   [Prior discussion of this topic (with links)]
   
   ## Rationale for involving the TOC
   [Why this topic would benefit from the TOC involvement]
   
   ## Resolution requested
   [The precise description of the request to the TOC, optionally including a specific decision to take if applicable. Note that the TOC doesn't have to take exactly that decision, it can modify it, or refuse it.]

Change History
**************

2023-05-18
==========

* Document created with rough first draft
* `Pull request #484 <https://github.com/openedx/open-edx-proposals/pull/484>`_ created
