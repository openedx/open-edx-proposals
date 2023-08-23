OEP-64: Mobile App Codebase Modernization
#########################################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-64 <oep-0064-arch-mobile-application-codebase-modernization>`
   * - Title
     - Mobile Application Codebase Modernization
   * - Last Modified
     - 2023-06-09
   * - Authors
     - 
       * Marco Morales <marco@schema.education>
       * Volodymyr Chekyrta <volodymyr.chekyrta@raccoongang.com>
   * - Arbiter
     - Ed Zarecor <ezarecor@axim.org>
   * - Status
     - Under Review
   * - Type
     - Architecture Decision
   * - Created
     - 2023-06-09
   * - Review Period
     - 2023-06-15 - 2023-08-01
   * - References
     - `Related Funded Contribution <https://openedx.atlassian.net/wiki/spaces/COMM/pages/3663429666/FC-0011+-+Mobile+Product+Strategy+Backlog+Development>`_

.. contents::
  :local:
  :depth: 1

Abstract
********

As part of the discovery effort done for a funded contribution (FC-0011) we are recommending that the Open edX platform adopt newly built iOS and Android applications as the new default mobile applications for the platform moving forward, starting with the Quince release. These new applications are rebuilt with Model View ViewModel (MVVM) architectural patterns and are fully rebuilt in languages native to the mobile platforms (Swift and Kotlin, respectively). These new code bases should facilitate faster development cycles, improved pluggability and community contribution rates. As of July 2023, not all functionality is 100% identical to the existing mobile applications, but efforts are underway to improve pluggability and close common community requirements for the mobile experience.

Motivation
**********

The motivation for this change is to drive adoption, contribution, and improvement of the Open edX platform mobile experiences. The existing mobile applications were authored in 2015 and are not broadly used across the Open edX ecosystem today. Improvements to architectural patterns, development languages, and mobile operating systems have transformed mobile development in ways that make it difficult for our existing code bases to adopt quickly. The Raccoon Gang mobile team rebuilt and open sourced new ios and android applications that form a strong architectural basis on which to continue community development of the mobile applications. 

A robust mobile experience makes learning opportunities more accessible whether that is for learners who don't have ready access to personal computers, or need to fit learning into their schedules around work and family commitments.


Specification 
************* 

The existing mobile repositories are maintained by edX / 2U and can be accessed here:

* https://github.com/openedx/edx-app-ios
* https://github.com/openedx/edx-app-android

* Support for the existing mobile applications will continue through the Redwood release. Starting with the Quince release, we will designate the new repositories as the default Open edX mobile applications to encourage adoption and use of the new applications for community team members.
* Once available, we will link to documentation within the new repositories to describe transition options for community members migrating from existing builds of the older codebases.
* After the publication of the Quince release we will move the original repositories or the openedx-unsupported github organization to help make clear that these are no longer the default mobile application builds moving forward. A corresponding DEPR ticket will be linked to this OEP once it exists. 

The new mobile repositories are maintained by Raccoon Gang and can be accessed here:
* https://github.com/raccoongang/educationx-app-ios
* https://github.com/raccoongang/educationx-app-android
* The new repositories will be migrated to the ``openedx`` github organization under the names ``openedx-app-ios`` and ``openedx-app-android``.
* The new repositories will be maintained in accordance with OEP-55.
* Relevant funded contributions and projects relation to the new mobile repositories will be linked from their main project README as a way to help community members find ongoing and future efforts.
* In support of a consistent platform-wide branding interface, the mobile experiences will strive to identify opportunities to consume and extend the Paragon design system, helping to ensure consistent and maintainable UX across channels (i.e., mobile, web).


Rationale
*********

Expanded access and opportunities for learners around the globe means meeting learners where they are, and for many people that means on their only digital device, their phone. Furthermore, enabling learners to continue to learn on their desktop or laptop computers in addition to a full featured mobile experience allows us to support full day learning workflows for those with multiple devices.

Similar to the way the edx-platform has undergone a modernization process over the past few years with updated build processes, micro services, micro frontends, design system tools and more, the mobile experiences will benefit from a modern architecture, being written in the latest development language tools, and set up for increased pluggability and contribution as well.


Reference Implementation
************************

The new mobile repositories are maintained by Raccoon Gang and can be accessed here:

* https://github.com/raccoongang/educationx-app-ios
* https://github.com/raccoongang/educationx-app-android

Rejected Alternatives
*********************

We considered exploring the development costs of incrementally improving the existing mobile applications to fully adopt new architectural patterns as well as the use of Swift and Kotlin. Efforts to use these new technologies go back many years, but it would take considerable effort for the community to support this migration. Realistically the current team at edX / 2U maintaining the repositories would have had to undertake a major development effort to achieve this transformation. 


Change History
**************

2023-06-09 
==========

* Document created, draft PR opened
* `Pull request #496 <https://github.com/openedx/open-edx-proposals/pull/496>`_
* Updated PR to reflect the latest OEP number and the named release planned
* Updates to match conventions and other OEP feedback