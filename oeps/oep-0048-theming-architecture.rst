
==============================
OEP-0048: Theming Architecture
==============================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0048 </oeps/oep-0048-arch-them>`
   * - Title
     - Theming Architecture
   * - Last Modified
     - <date string, in YYYY-MM-DD format>
   * - Authors
     - <list of authors' real names and optionally, email addresses>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - <Draft | Under Review | Deferred | Accepted | Rejected | Withdrawn | Final | Replaced>
   * - Type
     - <Architecture | Best Practice | Process>
   * - Created
     - <date created on, in YYYY-MM-DD format>
   * - Review Period
     - <start - target end dates for review>
   * - Replaces
     - OEP-23 Style Customization


Context
-------

Outcome:

- People who want to theme Open edX know how to do it, and why it's done this way
- It's clear that edX.org also uses this mechanism
- It is clear what theming means and does not mean

Definitions:


The ability to customize the Open edX platform to reflect a custom brand and visual style is a critically important feature for the Open edX community. The comprehensive theming system built into edx-platform has served theming needs for several years. It is imperfect and unofficially supported, but it has worked.

Meanwhile, the edX organization has invested in a new microfrontend architecture that features many small, independently deployable microfrontend applications (MFEs). By the end of this technical transition, most of the Open edX user interface will be delivered by these MFEs.

The UI in MFEs are primarily built using the Paragon SCSS and React library based on Bootstrap 4. Paragon itself supports themes via SCSS variable definitions in the same way as its parent library, Bootstrap. The edx.org theme lives inside the Paragon project along side a starter theme.

Today, MFEs hardcode the inclusion of the edx.org Paragon theme and are not easily themable. Why is this the case?

A key distinction between the MFEs edX has created and server-rendered pages is that MFEs are built, static applications. It may be better to think of MFEs as "web app generators" than web applications themselves. They are given a configuration as input and they output static html, javascript, and css.

Whereas a server application can read a configuration file when a user requests a page, an edX MFE does not. A pipeline builds and deploys the MFE with a configuration. If the configuration changes, the pipeline must be rerun and the MFE redeployed.

Because edX does not share a common build and deploy system with the Open edX community, MFEs lack the kind configurability available in Open edX's server applications. Work to create an MFE build pipeline for the community is currently underway.

This pipeline will unlock the ability to easily configure MFEs, but MFEs don't currently offer a way to configure a custom theme.

As MFEs serve more and more of the platform user interface, a system for theming them must be created. An ideal theming solution would apply to both MFEs and edx-platform itself.





- Theming on edX platform.
- Transition to microfrontends
- Styling of MFEs today. Paragon.
- What is Paragon. SCSS Framework and component library based on Bootstrap. Paragon is themeable in the way that Bootstrap is, via SCSS.
- The edx.org theme lives in the Paragon project and is hardcoded into with MFEs.

Theming system is required for Open edX operators to adopt microfrontends that are replacing portions of edx-platform.
Ideally the theming system applieds to both edx-platform and all mfes.


This section describes the forces at play, including technological, political,
social, and project local. These forces are probably in tension, and should
be called out as such. The language in this section is value-neutral. It is
simply describing facts.

Decision
--------

This section describes our response to the forces described in the Context.
It is stated in full sentences, with active voice. "We will ..."

.. image:: oep-0026/use_cases.png

Plan
----

Consequences
------------

This section describes the resulting context, after applying the decision.
All consequences should be listed here, not just the "positive" ones. A particular
decision may have positive, negative, and neutral consequences, but all of them
affect the team and project in the future.

References
----------

List any additional references here that would be useful to the future reader.
See `Documenting Architecture Decisions`_ for further input.

.. _Documenting Architecture Decisions: http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions
