.. _pep_based_template:

.. Below is the display in the left sidebar on RTD. Please omit leading 0's

OEP-66: OpenFeature Format For Feature Toggles
##############################################

.. This OEP template is based on Python's PEP standard.

.. list-table::
   :widths: 25 75

   * - OEP
     - Link to the doc in the following format::

        :doc:`OEP-0066 <oep-0066-bp-openfeature-toggles>`

   * - Title
     - Implement All Toggles Using OpenFeature Specification
   * - Last Modified
     - 2025-01-15
   * - Authors
     - Tobias Macey
   * - Arbiter
     - Feanil Patel <feanil@axim.org>
   * - Status
     - Draft
   * - Type
     - Best Practice
   * - Created
     - 2025-01-15
   * - Review Period
     - 2025-01-15 - 2025-02-28
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - `OEP 0017 <https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0017-bp-feature-toggles.html>`_

Abstract
********

OpenFeature is an open standard and ecosystem for managing feature toggles across
various languages and frameworks. This offers a single, consistent format for managing
the ubiquitous toggles in Open edX to improve their discoverability and maintenance.

Motivation
**********

OEP 17 provided an excellent and valid argument in favor of the adoption of feature
toggles as a core practice of the Open edX suite of software. Unfortunately, the
implementation of that practice has led to a large variety in the methods used to manage
those toggles, and dramatically different naming across repository boundaries. The
adoption of the `OpenFeature <https://openfeature.dev/>`_ specification and associated
SDKs allows us to be more consistent in the naming and management of those toggles.

The adoption of OpenFeature as the implementation target for Open edX software also
provides flexibility to operators of the software to use the toggle management service
that they prefer. This improves the ability of Open edX to fit into an existing
operations environment without forcing the site operators to conform to the use of
operations technologies that they are not familiar with.

Specification
*************

There are numerous pre-existing toggles across the Open edX ecosystem of projects. Many
of them are implemented as Waffle flags, but there has also been widespread use of
environment variables or directly setting application values via the various settings
interfaces. All of those existing mechanisms can co-exist with the OpenFeature
implementation, with OpenFeature becoming the entry-point and controller of the final
returned values. This provides a method of backward compatibility, while also offering
the ability to choose an alternative provider to manage the values of those toggles in a
running system.

Another challenge in the current state of feature toggles across Open edX projects is a
lack of naming consistency in the name of the actual toggle, along with a high degree of
variance with whether the toggle name has any relation to the feature being managed. In
the adoption of OpenFeature, the differently named toggles that share purpose across
repositories will be aligned. This will enable operators to manage a single toggle
setting to enable the functionality across the full Open edX stack.

Rationale
*********

Operating an Open edX installation is made more challenging by the confusion of
environment variables, Waffle flags, build arguments, etc. that need to be managed to
enable or disable the system's various features. By aligning the tooling across the
technology stacks involved it becomes easier to have consistent naming of toggles that
are better aligned with the actual feature being managed.

Backward Compatibility
**********************

In order to maintain backward compatibility with the current toggles and their
management interfaces (Django Waffle, environment variables, and application settings)
the OpenFeature configuration will default to use chained Providers that fall back to
the existing logic paths. At present there is not a provider implemented for Django
Waffle, but that will be created as part of the adoption process for this proposal.

Reference Implementation
************************

The reference implementation must be completed before any OEP is given "Final"
status, but it need not be completed before the OEP is "Accepted". While there is
merit to the approach of reaching consensus on the specification and rationale
before writing code, the principle of "rough consensus and running code" is
still useful when it comes to resolving many discussions.

Rejected Alternatives
*********************

The primary alternative is to maintain the status quo. That is a possibility, but one
that fails to address the challenges highlighted under "Motivation". Another alternative
is to use a different feature flag framework that supports both Python and JavaScript
runtimes. That is a viable approach, but runs the risk of vendor lock-in and the need
for a future migration that would be slow and costly.

Change History
**************

2025-01-15
==========

* Document created
* `Pull request #663 <https://github.com/openedx/open-edx-proposals/pull/663>`_
