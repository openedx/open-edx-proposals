.. _pep_based_template:

======================
OEP-XXXX: OEP Template
======================

.. This OEP template is based on Python's PEP standard.

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
========

The abstract is a short description of the technical issue that
this Open edX proposal (OEP) addresses.

Motivation
==========

The motivation is critical for OEPs that will change any part of the Open edX
ecosystem. Explain why the existing architecture or process is inadequate to
address the problem that the OEP solves, or why adopting the best practice
would significantly improve the Open edX world.

Specification
=============

The specification describes the technical details of the Architecture, Best
Practice or Process proposed by the OEP. If the proposal includes a new API,
specify its syntax and semantics.

Rationale
=========

The rationale adds to the specification by describing the events or
requirements that led to the proposal, what influenced the design, and why
particular design decisions were made. The rationale could provide evidence
of consensus within the community and discuss important objections or
concerns raised during discussion. It could identify any related work,
for example, how the feature is supported in other systems.

Backward Compatibility
======================

This statement identifies whether the proposed change is backward compatible.
An OEP that introduces backward incompatibilities must describe the
incompatibilities, with their severity and an explanation of how you propose to
address these incompatibilities.

Reference Implementation
========================

The reference implementation must be completed before any OEP is given "Final"
status, but it need not be completed before the OEP is "Accepted". While there is
merit to the approach of reaching consensus on the specification and rationale
before writing code, the principle of "rough consensus and running code" is
still useful when it comes to resolving many discussions.

Rejected Alternatives
=====================

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

Change History
==============

YYYY-MM-DD
----------

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_