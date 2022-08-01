###########################
OEP-47: Semantic Versioning
###########################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0047 <oep-0047-bp-semantic-versioning>`
   * - Title
     - Semantic Versioning
   * - Last Modified
     - 2020-05-28
   * - Authors
     - Robert Raposa <rraposa@edx.org>
   * - Arbiter
     - Régis Behmo <regis@behmo.com>
   * - Status
     - Accepted
   * - Type
     - Best Practice
   * - Created
     - 2020-03-19
   * - Review Period
     - 2020-05-11 - 2020-05-25

.. contents::
   :local:
   :depth: 3

Summary
*******

For any Open edX repository that publishes versioned packages, this OEP recommends following the `PEP 440`_ compatible sections of `Semantic Versioning v2`_.

The most relevant part from the summary of the `Semantic Versioning v2`_ specification:

    Given a version number MAJOR.MINOR.PATCH, increment the:

    * *MAJOR* version when you make *incompatible API changes*,
    * *MINOR* version when you *add functionality in a backwards compatible manner*, and
    * *PATCH* version when you make *backwards compatible bug fixes*.

From `PEP 440`_ (quote updated to match 2.0.0 of the spec):

    The "Major.Minor.Patch" (described in this PEP as "major.minor.micro") aspects of semantic versioning (clauses 1-8 in the 2.0.0 specification) are fully compatible with the version scheme defined in this PEP, and abiding by these aspects is encouraged.

Some additional tips are called out in:

* `Backward Incompatible Changes`_
* `Tooling and Automation`_
* `Versions for Forks`_

.. note::

    This OEP does not apply to repositories without versions. For example, services like edx-platform, ecommerce, etc. do not have versions.

.. _PEP 440: https://www.python.org/dev/peps/pep-0440/#semantic-versioning

Context
*******

Many or all of the Open edX installable libraries already use versions similar to the `Semantic Versioning v2`_ specification. This OEP simply codifies this best practice, and provides additional guidance.

Backward Incompatible Changes
*****************************

Special attention should be given to incompatible changes, because:

* Communicating incompatibilities alerts humans to possible problems when attempting to upgrade your package.
* A MAJOR version is what is used to communicate any "Backward Incompatible Changes" (also known as "Breaking Changes").

See related FAQ questions from `Semantic Versioning v2`_:

* `If even the tiniest backwards incompatible changes to the public API require a major version bump, won’t I end up at version 42.0.0 very rapidly?`_
* `What do I do if I accidentally release a backwards incompatible change as a minor version?`_
* `What if I inadvertently alter the public API in a way that is not compliant with the version number change (i.e. the code incorrectly introduces a major breaking change in a patch release)?`_

.. _If even the tiniest backwards incompatible changes to the public API require a major version bump, won’t I end up at version 42.0.0 very rapidly?: https://semver.org/#if-even-the-tiniest-backwards-incompatible-changes-to-the-public-api-require-a-major-version-bump-wont-i-end-up-at-version-4200-very-rapidly
.. _What do I do if I accidentally release a backwards incompatible change as a minor version?: https://semver.org/#what-do-i-do-if-i-accidentally-release-a-backwards-incompatible-change-as-a-minor-version
.. _What if I inadvertently alter the public API in a way that is not compliant with the version number change (i.e. the code incorrectly introduces a major breaking change in a patch release)?:


Tooling and Automation
**********************

* A checklist item in a pull-request template can remind people of any manual steps.
* Some frontend libraries have been using an npm package called `semantic-release`_. The `semantic-release`_ package automates incrementing the version appropriately for each PR merge, based on a well formatted commit comment.

  * One important formatting detail is to include "BREAKING CHANGE" at the beginning of a line in the commit description to get a new major version.

* Other tools can be added here over time.

.. _semantic-release: https://github.com/semantic-release/semantic-release

Versions for Forks
******************

We sometimes maintain forks of open source libraries. In these cases, the version should follow the `recommendation of the Python community`_, which is to add a `+edx.1` suffix to the end of the public version number.

.. _recommendation of the Python community: https://www.python.org/dev/peps/pep-0440/#local-version-identifiers

References
**********

* `Semantic Versioning v2`_

.. _Semantic Versioning v2: https://semver.org/spec/v2.0.0.html
