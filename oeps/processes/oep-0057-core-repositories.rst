===========================
OEP-0057: Core Repositories
===========================

.. list-table::
   :widths: 25 75

   * - OEP-0057
     - :doc:`OEP-0057 <oep-0057-core-repositories>`
   * - Title
     - Core Repositories
   * - Last Modified
     - 2022-03-XX
   * - Authors
     - Kyle McCormick <kyle@tcril.org>
   * - Arbiter
     - Joe Example <joe@example.com>
   * - Status
     - Draft
   * - Type
     - Process
   * - Created
     - 2022-03-XX
   * - Review Period
     - 2022-XX-XX - 2022-XX-XX

.. contents::
   :local:
   :depth: 3


Abstract
--------

TODO

.. note::

  This OEP is a big ol' work-in-progress. Feel free to leave early feedback, but please keep in mind that it's still a rough outline. Lots of this stuff is my stream-of-consciousness. It's not representative of tCRIL's or anyone else's plans/opinions. -Kyle


Motivation
----------

There is a desire to make the `openedx GitHub organization`_ easier to manage and understand. To do this, we must be judicious about introducing new repositories and consider removing some existing repositories. At the same time, contributors to new repositories are eager to share their work with the Open edX community and, understandably, often desire for the upstream of their repository to become reflected under the GitHub organization that bears the Open edX project's name.

To best accomodate both these interests in a fair and inclusive way, this OEP aims to:

* establish the purpose of the openedx GitHub organization,
* define a transparent process for the management of the organization's set of repositories, and
* explain how community members can prepare their repositories for inclusion in the organization.

Definition of Core Repositories
-------------------------------

* We define the *Core repositories of Open edX* (or in this context, "the Core") as the entire set of public repositories hosted in the `openedx GitHub organization`_.

  * Phrased another way: A repository is in the Core if and only if it is public and in the openedx GitHub organization.

  * Terminology notes:

    * The choice of *Core* here is inspired by the Core Contributor program, which exists in part to grant community members access to repositories in the openedx GitHub organization.

    * This does not preclude other, smaller "cores" of Open edX (such as a *learning core*, *core utilities*, etc.) from existing within the Core repositories.

  * **What repositories are in the Core?**

    * A repository *must* be in the Core if it is directly included in `latest Open edX release`_.

    * A repository *must* be in the Core if:

      * it is listed as a non-optional base dependency of a repository included in the latest Open edX release *and*

      * it is *not* maintained as a third-party package that is unspecific to Open edX, such as Django or Python Requests.

    * In addition to the repositories that must be in the Core, a repository *may* be in the Core if:

      * it is of interest to more than one entity the Open edX community (TODO: this needs better criteria),

      * it has assigned `maintainers`_ who strive to:

        * review community code contributions,
        * participate in community discussions related to the repository, and
        * keep the repository compliant with the accepted Open edX Proposals (OEPs).

* We declare that all access to write, configure, or otherwise administer Core repositories shall be granted through the `Core Contributor Program`_ and managed by its Program Administrators.

* We declare that the addition or removal of repositories from the Core should by reviewed by existing Core Contributors.

  * TODO: Define a review process.


Reasoning
---------

Many other repositories exist as part of the Open edX community. Some of these are forks of Core repositories, and others are upstream repositories in their own right. These various non-Core repositories are vital to different Open edX community members. Furthermore, the existence of non-Core repositories in general is important to the health of the Open edX project, which thrives on collaboration, experimentation, and extension.

However, from these many repositories, we wish to enumerate a smaller set which:

* is a good target for enhancement, documentation, and technical investment;

* defines a logical boundary within which it is fair to enforce Open edX Proposals;

* defines an administrative boundary within which the Core Contributor Program applies;

* makes the Open edX project more comprehensible by collecting its most critical components;

* (TODO: should I add something about tCRIL stewardship/ownership?); and

* encompasses the first-party aspect of the Open edX release supply chain, identifying repositories that need to be:
  * monitoried for discloures under the `Open edX security policy`_,
  * regularly updated with security patches,
  * covered by automated unit tests and static analysis, and
  * ready to deploy to production at any time.


Considerations
--------------


* Resolving legacy processes and access policies

  * The process for reviewing addition/removal of repos via the Core Contributor program isn't ready to be spun up yet or even well-defined. In the meantime, tCRIL has been making these judgement calls.
  * 2U OCM and some of tCRIL retain historical access to many Core repositories outside of the Core Contributor program process. There is a plan to bring those folks under the Core Contributor umbrella, but it will take time.
  * tCRIL is currently using a few public repositories in the openedx organization for day-to-day work, such as ``openedx/tcril-engineering``. Upon acceptance of this OEP, they would need to make a new GitHub organization for those repositories.

* Cleaning up the Core

  * Many repositories in the openedx GitHub organization do not currently meet the Core criteria. We will need to review the organization contents, and for several repos, transfer them out or invest in them.
  * Even so, the definition of the Core still captures a large number of repositories, probably larger than we want our "Core" to be. We could reduce it by loosening the package dependencies of many services to being optional instead of strictly required.

* Ensuring the Core contains everything it should

  * There are four repositories directly included in Maple that aren't in the openedx organization. We'll either need to remove them from the release or transfer them into openedx.
  * This will take a while, but we'll need to look at the list of packages that are depended upon by released repositories. If any of them are Open edX code but outside the openedx organization, we'll need to move them into openedx, or refactor the dependencies so that they're optional.



Motivation
----------

TODO


Alternatives Considered
-----------------------

TODO


Change History
--------------

2022-03-XX
==========

* Document created in `initial pull request`_.


.. _openedx GitHub organization: https://github.com/openedx
.. _security policy: https://github.com/openedx/edx-platform/security/policy
.. _latest Open edX release: ./oep-0010-proc-openedx-releases.rst
.. _maintainers: ./oep-0055-project-maintainers.rst
.. _Core Contributor Program: ./oep-0054-core-contributors.rst
.. _initial pull request: https://github.com/openedx/open-edx-proposals/pull/312
