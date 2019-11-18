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
     - 2019-11-14
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

Each repo will include a file ``openedx.yaml``, with the following keys:

``owner``: string (required unless ``archived`` is ``True``)
    The GitHub user responsible for the repository.

``supporting_teams``: list of strings (optional)
    The GitHub teams supporting the owner for this repository.

``nick``: string (optional)
    A short-name for the repository used by reporting tools created by the edX Open Source team. Should be 3-4 characters, because this value is used in charts with very limited space on their axes.

``tags``: list of strings (optional)
    Used to group repositories by in reporting tools. Sample tags: core, mobile analytics.

``oeps``: dictionary (optional)
    A list of keys corresponding to Best Practice OEPs (in the format ``oep-n``). Each value would be one of ``True``, ``False``, or a reason dictionary. ``True`` or ``False`` indicate compliance (or lack thereof) with the specified OEP.

    A reason dictionary provides more detailed information. It can contain a boolean ``state``, a boolean ``applicable``, and a string ``reason``.

    -  If the OEP is not currently applicable to this repository, the reason dictionary can simply contain a single value of ``False`` for the ``applicable`` key.  For example: an OEP that defines best practices for JavaScript testing would be marked as not applicable in a Python command line tool's repository.
    -  If the ``state`` is ``False`` a ``reason`` value must be included that explains why.
    -  If the ``state`` is ``True`` or ``applicable`` is ``False`` the ``reason`` can optionally provide more information. The ``reason`` value will be displayed in reporting tools. If an OEP isn't listed in the ``oeps`` dictionary, then it is assumed to be ``False``, unless the reporting tool can auto-detect accordance.

``track-pulls``: obsolete
    This key is obsolete, please remove if found.

``openedx-release``: defined by :doc:`OEP-10 <oep-0010-proc-openedx-releases>`.
    Define this key if your repo is an application or IDA that is part of Open edX releases.  Omit this key if your repo is a library, or is not part of Open edX releases.  See :doc:`OEP-10 <oep-0010-proc-openedx-releases>` for details.

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
    The repository contains shared functionality that form a common framework used by multiple services or apps in the platform.

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
    owner: nasthagiri
    supporting_teams:
        - platform-core
        - platform-core-extensions
        - platform-authn
        - platform-courseware
    nick: edx
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

The keys in ``openedx.yaml`` were derived from existing repository metadata collected by edx.org.

The design of the ``oeps`` dictionary was guided by a couple of use cases:

1. Adding a new OEP that few repositories will support, initially. We shouldn't need to update all repositories to detect if they are in accordance, we should be able to assume that they aren't, or automatically detect whether they are.
2. Repositories may have specific requirements that force them to not implement a best practice. The tools should be able to present that reasoning to anyone looking across repositories, and the reasons should be documented in the repositories themselves.
3. As much as possible, Best Practices should be autodetected, but because they will often involve a judgement call, autodetection shouldn't be mandatory.


Change History
==============

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
