OEP-21: Deprecation and Removal
###############################

.. list-table::
   :widths: 20 80
   :header-rows: 0

   * - OEP
     - :doc:`OEP-21 <oep-0021-proc-deprecation>`
   * - Title
     - Deprecation and Removal
   * - Last Modified
     - 2025-04-07
   * - Authors
     - | Greg Sham <gsham@edx.org>,
       | Nimisha Asthagiri <nimisha@edx.org>,
       | Diana Huang <dkh@edx.org>,
       | Sarina Canelake <sarina@axim.org>
       | Feanil Patel <feanil@axim.org>
   * - Arbiter
     - | Matt Tuchfarber <mtuchfarber@edx.org>
   * - Status
     - Accepted
   * - Type
     - Process
   * - Created
     - 2018-05-18
   * - Review Period
     - 2018-06-06 - 2018-06-20

Abstract
********

This document defines a formal process for proposing, communicating, deprecating, and removing legacy and unneeded code in the Open edX platform.

Motivation
**********

Without a clear process for removing legacy code, developers hesitate to do so, leading to redundant implementations and dormant features that slow down development.

Benefits of code removal include:

* Reducing maintenance burden and costs [OREILLY]_
* Improving project learnability and navigation [OREILLY]_
* Enabling better refactoring and optimization [OREILLY]_
* Preventing potential bugs from dormant code [SO]_ [SEC]_
* Reducing runtime footprint [INFOQ]_
* Encouraging continuous improvement [INFOQ]_

High-Level Overview of Code Removal Process
===========================================

.. graphviz::

   digraph process {

       node [fontsize=20, shape = box];

       Draft -> RFC [label=" Someone is ready to own the ticket and does RFC work"];
       RFC -> "Plan Accepted" [label=" 2 weeks (negotiable)"];
       "Plan Accepted" -> "Transition Unblocked"
       "Plan Accepted" -> "Breaking Changes Unblocked" [label=" 6 months (negotiable)"]
       "Transition Unblocked" -> "Breaking Changes Unblocked" [label=" 1 month (negotiable)"];
       "Breaking Changes Unblocked" -> "Plan Completed"
   }

Process States
==============

The following sections describe each state in the deprecation and removal process.

Draft
-----

Initial state where code is identified for removal but no one has committed to coordinating the process. A ticket created with the "Deprecation (DEPR) Ticket" template remains in Draft until someone claims it as coordinator.

When to Consider Removal
~~~~~~~~~~~~~~~~~~~~~~~~

* New implementation replaces old functionality
* Static/runtime analysis shows code is never executed
* Feature has very low adoption

What Can Be Removed
~~~~~~~~~~~~~~~~~~~

* User features (APIs, frontend, backend)
* Modular components (Django apps, Frontend apps, XBlocks)
* Outdated technologies or frameworks
* Temporary feature toggles
* Deprecated interfaces

RFC (Request for Comments)
--------------------------

A coordinator has claimed the ticket and is actively gathering community feedback. During this phase:

1. Announce the proposed deprecation on:

   - `Open edX Discourse Deprecation Announcements category`_
   - Slack channels `#general`_ and any relevant working groups (e.g., `#wg-frontend`_, `#wg-build-test-release`_, `#wg-maintenance`_)

2. Allow time for feedback (typically 2 weeks, but negotiable)

3. Respond to concerns and update the proposal as needed

If significant issues arise, be willing to extend the feedback period or revise the plan. For complex cases, consider consulting the Deprecation Working Group for guidance.

.. _Open edX Discourse Deprecation Announcements category: https://discuss.openedx.org/c/announcements/deprecation/20
.. _#general: https://openedx.slack.com/archives/C02SNA1U4
.. _#wg-frontend: https://openedx.slack.com/archives/C04BM6YC7A6
.. _#wg-build-test-release: https://openedx.slack.com/archives/C049JQZFR5E
.. _#wg-maintenance: https://openedx.slack.com/archives/C03R320AFJP


Plan Accepted
-------------

After the RFC period and once all community concerns are addressed, the coordinator updates the ticket to "Plan Accepted" status. At this point:

1. The deprecation plan is officially approved
2. No new usage of the deprecated code should be introduced
3. Code should be marked as deprecated:

   - REST API: Add to docstring `"Deprecated <link-to-gh-issue>"`
   - JavaScript: `console.log("<Technology name> is deprecated. See <link-to-gh-issue>.")`
   - Python: `warnings.warn("<Technology name> is deprecated. See <link-to-gh-issue>.", DeprecationWarning)`
   - Feature toggles: Set expiration date as in :ref:`OEP-17 <OEP-17>`
   - GitHub repo: Follow :ref:`OEP-14 <OEP-14>` for archiving

For larger changes, include a link to the deprecation ticket in the release notes for the next named release.

After acceptance, the ticket will proceed to either:

- "Transition Unblocked" when a replacement is ready, or
- Stay in "Plan Accepted" and wait for the negotiated timeline (default: 6 months) before proceeding to "Breaking Changes Unblocked"

Transition Unblocked
--------------------

This state indicates that a replacement for the deprecated code is ready and available. During this period:

1. Both old and new implementations operate in parallel
2. Users have time to transition to the new implementation (default: 1 month)
3. Feature flags/toggles control which implementation is active

The DEPR ticket should clearly communicate:

- How to enable/disable each implementation
- Any changes to default settings
- Documentation for the new implementation

This transition period provides a safe migration path before breaking changes occur.

Breaking Changes Unblocked
--------------------------

In this state, support for the old implementation has been officially dropped and developers can begin removing code. This state can be reached either:

1. Directly from "Plan Accepted" after a negotiated waiting period (default: 6 months), or
2. From "Transition Unblocked" after the transition period (default: 1 month)

During this phase:

- Remove related code from frontends, APIs, and backends
- Remove any feature flags introduced during transition
- Update documentation to remove references to the old implementation
- Keep the DEPR ticket updated with progress and any issues encountered

Users should expect the deprecated code to stop working at any point during this phase.

Plan Completed
--------------

This is the final state, reached when all aspects of the deprecation and removal plan have been completed. When removal is complete:

1. Add the DEPR ticket to the wiki page for the next Open edX named release
2. Update the DEPR ticket's state to "Plan Completed" with a final comment
3. Announce completion in the #wg-depr-slash-and-burn Slack channel

This state marks the successful conclusion of the deprecation and removal process.

Timeline
--------

The deprecation process follows these default timeframes, though all are negotiable based on complexity and impact:

* **Draft to RFC**: When a coordinator claims the ticket
* **RFC Period**: 2 weeks for community feedback
* **Plan Accepted to Breaking Changes Unblocked**: 6 months (if no replacement needed)
* **Transition Unblocked Period**: 1 month for users to migrate to the new implementation
* **Breaking Changes to Plan Completed**: Varies based on removal complexity

When scheduling removal, consider the Open edX named release cycle (currently 6 months). The 6-month default waiting period ensures that operators have some warning before functionality is removed.

References
**********

.. [OREILLY] https://www.oreilly.com/library/view/becoming-a-better/9781491905562/ch04.html
.. [SO] https://stackoverflow.com/a/15700228
.. [SEC] https://www.sec.gov/litigation/admin/2013/34-70694.pdf
.. [INFOQ] https://www.infoq.com/news/2017/02/dead-code

Change History
**************

2025-04-15
==========
* Updated process with new workflow states
* Simplified document for greater clarity and conciseness

2024-04-12
==========
* Changed process to require every DEPR have a coordinator.

2023-06-21
==========
* Update Discourse template to describe how to stay up to date on removal status.

2022-11-01
==========
* Simplified guidance around the earliest named release and removal dates.
* Clarify that proposal can be made while incomplete, as long as they aren't communicated until they are complete.

2022-07-05
==========

* Add suggestion for monitoring.

2022-06-03
==========

* Suggest alignment with release cycle
* `Pull request #252 <https://github.com/openedx/open-edx-proposals/pull/252>`_

2022-02-08
==========

* Migrate from Jira to GitHub Issues/Projects
* `Pull request #280 <https://github.com/openedx/open-edx-proposals/pull/280>`_

2021-04-26
==========

* "Removal dates" has become synonymous with "Accepted dates", so we are
  eliminating them to clarify the process.
* `Pull request #207 <https://github.com/openedx/open-edx-proposals/pull/207>`_

2021-02-05
==========

* Added TL;DR section and workflow visuals
* `Pull request #176 <https://github.com/openedx/open-edx-proposals/pull/176>`_

2019-01-02
==========

* Recommend codebase impact review as part of DEPR process
* `Pull request #95 <https://github.com/openedx/open-edx-proposals/pull/95>`_

2018-11-02
==========

* Describes the process in specific ordered steps for one to follow.
* Adds references to external docs.
* Updates information on the JIRA ticketing process.
* `Pull request #83 <https://github.com/openedx/open-edx-proposals/pull/83>`_

2018-06-21
==========

* Document created
* `Pull request #63 <https://github.com/openedx/open-edx-proposals/pull/63>`_
