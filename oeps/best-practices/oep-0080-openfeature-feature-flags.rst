.. _external_link_template:

.. Below is the display in the left sidebar on RTD. Please omit leading 0's

OEP-80: OpenFeature Feature Flags
#################################

.. This OEP template is recommended when content is externally linked.

.. list-table::
   :widths: 25 75

   * - OEP
     - Link to the doc in the following format::

        :doc:`OEP-XXXX <oep-XXXX-YYYY-ZZZZ>`

        * <XXXX is the next available OEP number>
        * <YYYY is the abbreviated Type: proc | bp | arch>
        * <ZZZZ is a brief (< 5 words) version of the title>

   * - Title
     - OpenFeature Feature Flags
   * - Last Modified
     - 2024-12-13
   * - Authors
     - Christ Patti <cpatti@mit.edu>, Tobias Macey <tmacey@mit.edu>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - <Draft
   * - Type
     - Best Practice
   * - Created
     - 2024-12-13
   * - Review Period
     - <start - target end dates for review>

Abstract
********

Right now OpenEdX has a huge array of toggles and feature flags in a number of repositories and using a number of formats.

Discoverability and interop with standard tools is poor, and the current system is not well documented.

Using OpenFeature for all our toggles across the board would allow us to use tools like Posthog which would <Why we use Posthog? $REASONS?)

References
**********

.. _OpenFeature Website: https://openfeature.dev
.. _OpenFeature Specification: https://openfeature.dev/specification/

Decisions
*********

OpenEdX should use Openfeature for all its toggles and feature flags.

Change History
**************

YYYY-MM-DD
##########

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
