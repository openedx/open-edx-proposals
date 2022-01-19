======================
OEP-XXXX: Micro-frontend Plugins
======================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-XXXX <oep-XXXX-arch-micro-frontend-plugins>`
   * - Title
     - Micro-frontend Plugins
   * - Last Modified
     - 2021-11-03
   * - Authors
     - David Joy <djoy@edx.org>
   * - Arbiter
     - ???
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2021-11-03
   * - Review Period
     - <start - target end dates for review>
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - <links to any other relevant discussions or relevant related materials>

Abstract
========

The ability to extend the user interface of the Open edX Platform is a prerequisite for scalable innovation, customization, and experimentation. It allows us to maintain a small, stable, and _extensible_ platform core, and is a vital part of our micro-frontend customization capabilities as outlined in OEP-XXXX: Micro-frontend Customization.

Micro-frontend plugins (a.k.a., "Experience Plugins" or "XPs") give us a dramatic increase in flexibility in how we build and deploy our user experiences.  They enable an increase in team autonomy for feature development, modularization of cross-cutting features, code reuse, and approachable experimentation and extensibility.

They may be implemented with variety of technologies, such as LTI, plain iframes or federated modules. This document outlines the various classifications of frontend plugins, describes the appropriate use cases for each, and serves as a set of architectural guidelines on how our frontend extensibility framework should be structured.  The goal of this is to keep this framework consistent and well-documented while being flexible and extensible as it expands to cover more and more use cases.

Motivation
==========

Micro-frontend plugins will allow operators to customize and extend the micro-frontends of their site according to their own business, course team, and learner needs.  The most rudimentary alternative is to ask everyone to maintain their own forks of all the micro-frontend repositories, which significantly increases the time it takes to maintain their sites, keep them up to date, and guard against regressions.  It also decreases adoption of the latest Open edX releases, as the barrier to entry is that much higher.

Creating a mechanism by which we can add loosely-coupled plugins into the UI without modifying the core micro-frontend code solves all these problems and also gives us a ready-made mechanism to modularize our code, decouple our features and domains, and keep the core small and stable.

Last but certainly not least, we also believe that enabling and empowering experimentation and innovation in the platform's user experiences is one of the most powerful levers we have to improve learner satisfaction and outcomes.

Specification
=============

For the purposes of this document, we're going to discuss three primary technologies/standards for micro-frontend plugins.  Those technologies are Webpack module federation, inline frames (iframes), and the Learning Tools Interoperability (LTI) standard.

Note: It's worth pointing out that LTI can use - but does not require - iframes in order to establish a security sandbox.  When we refer to iframes or iframing in this document, that means using an iframe _without_ LTI unless otherwise noted.

The most important considerations when discussing micro-frontend extensibility are security, authentication, trust boundaries and user experience performance.  If we approach plugins from a security-first perspective, our implementation requirements change drastically depending on whether or not the author is inside or outside our trust domain and whether or not the plugin needs to support authenticated access to the platform's APIs - assuming we're also trying to optimize user experience performance where possible.

Security
--------

Authentication
--------------

Trust boundaries
----------------

UX Performance
--------------

Module Federation (Webpack)
---------------------------

Inline Frames (iframes)
-----------------------

Learning Tools Interoperability (LTI)
-------------------------------------

Framework Entities
------------------

Plugin Slots
************

Plugin Loaders
****************

Plugin Providers


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

A list of dated sections that describes a brief summary of each revision of the
OEP.
