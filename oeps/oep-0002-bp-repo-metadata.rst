==========================
OEP-2: Repository Metadata
==========================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-2 <oep-0002-bp-repo-metadata>`
   * - Title
     - Repository Metadata
   * - Last Modified
     - 2020-08-17
   * - Authors
     - Calen Pennington, Feanil Patel, Nimisha Asthagiri
   * - Arbiter
     - Calen Pennington <cale@edx.org>
   * - Status
     - Accepted
   * - Type
     - Best Practice
   * - Created
     - 2016-04-21

Abstract
========

Proposes a standard format for repository metadata for Open edX projects.

Motivation
==========

One of the primary goals for Best Practice OEPs is to standardize techniques and tools across Open edX repositories. However, without tooling, it will be difficult to move all of the repositories in the same direction (and hard to validate which ones have been updated). This OEP exists to fill that gap.  By providing a standard format for metadata about accordance to Best Practice OEPs, tools can be developed in the future to report on the current status of all of the Open edX repositories, and how well they follow the Best Practices listed in the OEPs.

Bear in mind that particular classes of Open edX repositories may have different Best Practices. The practices that apply to an XBlock may not be the same as those that apply to a standalone Django service or to a Django app.

Specification
=============

Keys
----

Each repo will include a file ``openedx.yaml``, with the following keys:

``tags``: list of strings (optional)
    This key is used to annotate repositories, for example to categorize them for automated reporting tools. See Tags_.

``oeps``: dictionary (optional)
    A list of keys corresponding to Best Practice OEPs (in the format ``oep-n``). Each value would be one of ``True``, ``False``, or a reason dictionary. ``True`` or ``False`` indicate compliance (or lack thereof) with the specified OEP.

    A reason dictionary provides more detailed information. It can contain a boolean ``state``, a boolean ``applicable``, and a string ``reason``.

    -  If the OEP is not currently applicable to this repository, the reason dictionary can simply contain a single value of ``False`` for the ``applicable`` key.  For example: an OEP that defines best practices for JavaScript testing would be marked as not applicable in a Python command line tool's repository.
    -  If the ``state`` is ``False`` a ``reason`` value must be included that explains why.
    -  If the ``state`` is ``True`` or ``applicable`` is ``False`` the ``reason`` can optionally provide more information. The ``reason`` value will be displayed in reporting tools. If an OEP isn't listed in the ``oeps`` dictionary, then it is assumed to be ``False``, unless the reporting tool can auto-detect accordance.

``openedx-release``: defined by :doc:`OEP-10 <oep-0010-proc-openedx-releases>` (optional)
    Define this key if your repo is an application or IDA that is part of Open edX releases.  Omit this key if your repo is a library, or is not part of Open edX releases.  See :doc:`OEP-10 <oep-0010-proc-openedx-releases>` for details.

Obsolete Keys
*************

``track-pulls``:
    This key is obsolete, please remove if found.

``supporting_teams``: list of strings
    This is an obsolete key that was used to specify Github handles for teams supporting the owner of the repository. This is now deprecated in favor of the updated ``owner`` dictionary. As a reasonable migration of this key, set ``owner.type`` to *team*, ``owner.value`` to the first value in ``supporting_teams``, and then remove ``supporting_teams``.

``owner``: dictionary
    This key used to contain information about the assigned owner of this repository.

    ``type``: string (optional)
        The value of this key would be either ``team`` or ``repo``. It indicated which ownership model applied to this repository, and therefore which of these two keys would exist with a non-empty value.

    ``team``: string (optional)
        The GitHub team responsible for this repository, prefixed with the Github organization (e.g., ``edx/arch-bom``). If this value was set, this repository contained high-level, business-aligned functionality that was suitable for direct ownership designation to an individual responsible team.

    ``repo``: string (optional)
        The GitHub repo responsible for this repository, prefixed with the Github organization (e.g., ``edx/XBlock``). If this value was set, this repository contained lower-level technical functionality that was affiliated with another primary owning higher-level repository.

``nick``:
    This key is obsolete, please remove if found. It was a short-name for this repository, used by reporting tools.

``archived``: boolean (optional)
    If ``True``, this specifies that this repository is archived and no longer maintained by edX.


Tags
----

The ``tags`` structure is very open ended but there are a few tags that are currently in wide use and are provided here so that they can be used consistently.

``webservice``:
    The repository is the root of a webservice that serves HTTP content.

    Code runs in production web servers and has publicly accessible views/APIs for end-users.

    eg. credentials

``frontend-app``:
    The repository primarily contains code for a frontend application.

    Code runs in production devices (such as web browsers and mobile devices) and is publicly accessible by end-users.

    eg. frontend-app-profile, edx-app-ios

``framework``:
    The repository contains shared functionality that forms part of a common framework used by multiple services or apps in the platform.

    Code runs in production, behind the scenes, (intended to be) globally used throughout the platform.

    eg. edx-drf-extensions, edx-ace, frontend-platform, paragon

``backend-service``:
    A service that is run as part of the platform but is not directly accessed by end-users.

    Code runs in production backend servers, typically within the firewall, and provides APIs for access by public-facing ``webservices``.

    eg. xqueue-watcher, ecommerce-worker

``backend-tooling``:
    The repository contains scripts or configuration used in conjunction with backend services or in support of openedx.

    Code does *not* run in production.

    eg. ecommerce-scripts, repo-tools, testeng-ci

``library``:
    The repository is an installable package that is reusable but doesn't run standalone. eg. a pip installable python package

    Code runs in production, behind the scenes, locally used by a part of the platform.

    eg. frontend-component-footer, edx-milestones, user-util

``xblock``:
    The repository contains an xblock or xblock related tooling.

    Code runs in LMS and Studio as part of course run content.

    eg. xblock-review, staff_graded-xblock

Example
-------

For example, in the `edx-platform`_ repo, the file might look like:

.. _edx-platform: https://github.com/edx/edx-platform

.. code-block:: yaml

    # openedx.yaml

    ---
    tags:
        - core
        - xblock
        - lms
        - studio
    oeps:
        oep-314: True  # edx-platform uses django 3.14
        oep-42:
            state: False
            reason: This OEP doesn't actually exist
        oep-2:
            state: True  # no reason is required since this is True
        oep-100:
            applicable: False  # state is not required since the OEP is not applicable
            reason: This OEP contains best practices for C++ which is not used in edx-platform
        oep-101:
            applicable: False  # reason is not required since it's almost always just a redundant statement about it not being applicable


Rationale
=========

Keys Rationale
--------------

The keys in ``openedx.yaml`` were derived from existing repository metadata collected by edx.org.

OEPs Rationale
--------------

The design of the ``oeps`` dictionary was guided by a couple of use cases:

1. Adding a new OEP that few repositories will support, initially. We shouldn't need to update all repositories to detect if they are in accordance, we should be able to assume that they aren't, or automatically detect whether they are.
2. Repositories may have specific requirements that force them to not implement a best practice. The tools should be able to present that reasoning to anyone looking across repositories, and the reasons should be documented in the repositories themselves.
3. As much as possible, Best Practices should be autodetected, but because they will often involve a judgement call, autodetection shouldn't be mandatory.

Change History
==============

2021-05-26
----------

* Move the ``archived`` key to the `Obsolete Keys`_ section.

2020-10-13
----------

* Move the ``nick`` key to the `Obsolete Keys`_ section.

2020-08-17
----------

* Move the ``owner`` key to the `Obsolete Keys`_ section and removed no longer relevant ownership rationale and information.

2020-06-08
----------

* Make the ``owner`` key optional. Initially, the key served two purposes: assigning ownership of the repository and as a "who to contact" for repo related issues. Ownership info has now been moved to an edX internal location. As for contact info, this is still a work-in-progress. We plan on adding contact point to repos at some point in the future. The exact location is still to be determined, possibly in openedx.yaml or CODEOWNERS file.

2019-12-11
----------

* New practices for ownership bookkeeping and designation in our repositories:

  * Ownership is assigned to squads (i.e., teams with 5-6 people each) as opposed to individuals.
  * Owners are assigned at the granularity of higher-level, business-aligned functionality (user-facing services, user-facing apps, and system-wide frameworks).
  * Lower-level repos are indirectly assigned owners through assignment to dependent higher-level repos.

2019-10-29
----------

* Minor formatting and wording changes for clarity.

2017-01-18
----------

* Change ``obsolete`` to ``archived`` and relax the requirement for an owner if ``archived`` is True.

2017-01-10
----------

* Support the ``applicable`` key in the reason dictionary.

2016-10-13
----------

* Move the definition of ``openedx-release`` to :doc:`OEP-10 <oep-0010>`.

2016-08-24
----------

* Add documentation of the ``openedx-release``, ``track-pulls``, ``dead``, and ``nick`` keys in the ``openedx.yaml`` file.

2016-06-29
----------

* Original publication
