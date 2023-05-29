.. _pep_based_template:

.. Below is the display in the left sidebar on RTD. Please omit leading 0's

OEP-63: TOC Arbitration
#######################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0063 <oep-0063-proc-toc-arbitration>`
   * - Title
     - TOC Arbitration
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
   * - Resolution
     - TBD (include links to public discussions)
   * - References
     - `TOC meeting notes - governance norms <https://discuss.openedx.org/t/2023-04-11-toc-meeting-summary/10019#governance-norms-5>`_

Abstract
********

The Technical Oversight Committee (TOC) aims to empower the project's contributors to take decisions. The person closest to the work will often take the best decisions, and empowered contributors face less friction. 

However, some of the decisions can sometimes be complex: lack of consensus, decisions with long-lasting reprecussions. This can result in delaying taking *any* decision, which can be more hurtful than any of the choices.

In this OEP, the TOC defines a process for community members to bring up topics to the board's attention, and request to take a decision about a proposal (an "Arbitration"). 

Motivation
**********

The TOC discussed during the April 2023 meeting a `series of guidelines for operating and taking decisions <https://discuss.openedx.org/t/2023-04-11-toc-meeting-summary/10019#governance-norms-5>`_. This includes establishing a way for the community to use the TOC to help take decisions, which this OEP implements.

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

- Requests for arbitration can be posted by anyone in the community - but need to be supported by at least two core contributors or a TOC member
- The TOC members are encouraged to participate in community discussions early on, before the TOC is asked to arbitrate.

Step-by-step - How to submit an arbitration
===========================================

- Open a discussion in a public place, such as the `forum <discuss.openedx.org/>`_ or using one of the formal decision recording formats such as `OEPs <https://open-edx-proposals.readthedocs.io/en/latest/>`_. The topics presented need to have been discussed in the community before being formally considered by the TOC.
- After public review, post a formal arbitration request to the TOC. This can be done in a forum post or a `github ticket <https://github.com/openedx/wg-coordination/issues/new>`_ mentioning the TOC chair (@e0d). See the format below.
- Core contributors and/or TOC members supporting the arbitration reply in the thread to say so.
- If the requirements are met, the TOC will schedule the topic for a future meeting. 
- If the arbitration contains a suggested decision, the TOC can choose to accept the suggested decision, with or without changes, or to refuse it. 
- The outcome of the arbitration is communicated as a reply to the forum thread of the arbitration, with a comment to explain the decision.

Arbitration - Post template (forum or github)
=============================================

.. code-block:: none

   Subj: TOC Arbitration - [Title for the arbitration]
   Category: Community
   
   ---------------------
   
   ## [Title for the arbitration]
   
   ## Summary 
   [Prior discussion of this topic (with links)]
   
   ## Rationale for involving the TOC
   [Why this topic would benefit from the TOC involvement]
   
   ## Decision requested
   [The precise description of the decision that is requested from the TOC. Note that the TOC doesn't have to take exactly that decision, it can modify it, or refuse it.]

Change History
**************

2023-05-29
==========

* Changed the name of the procedure from "appeal" to "arbitration" to address review comments.

2023-05-18
==========

* Document created with rough first draft
* `Pull request #484 <https://github.com/openedx/open-edx-proposals/pull/484>`_ created
