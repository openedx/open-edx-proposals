###########################
OEP-47: Semantic Versioning
###########################

.. list-table::

   * - OEP
     - :doc:`OEP-0047 </oeps/oep-0047>`
   * - Title
     - Changelogs
   * - Last Modified
     - 2020-04-30
   * - Authors
     - Robert Raposa
   * - Arbiter
     - ???
   * - Status
     - Draft
   * - Type
     - Best Practice
   * - Created
     - 2020-03-19
   * - Review Period
     - ???

.. contents::
   :local:
   :depth: 3

Summary
=======

This OEP recommends following the `Semantic Versioning v2`_ specification when versioning any Open edX installable libraries.

The most relevant part from the summary of the `Semantic Versioning v2`_ specification:

    Given a version number MAJOR.MINOR.PATCH, increment the:

    * *MAJOR* version when you make *incompatible API changes*,
    * *MINOR* version when you *add functionality in a backwards compatible manner*, and
    * *PATCH* version when you make *backwards compatible bug fixes*.

Some additional tips are called out in `Backward Incompatible Changes`_ and `Tooling and Automation`_.

Context
=======

Many or all of the Open edX installable libraries already use versions similar to the `Semantic Versioning v2`_ specification. This OEP simply codifies this best practice, and provides additional guidance.

Backward Incompatible Changes
=============================

In addition to the general rule for incrementing versions in the `Summary`_, here is some additional information about backward incompatible versions.

Backward incompatible changes always result in a new MAJOR version, and not a MINOR or PATCH version. This is important because it alerts humans to learn about the change before upgrading.

* Even if you think the breaking change is unlikely to affect anyone, you can simply note that fact in the changelog entry.
* If you later find a backward incompatible change in a MINOR or PATCH version after publishing the package, please update the changelog and communicate the inconsistency. Additionally, you could do one of the following:

    * A new MINOR or PATCH version could be released to revert the change, and the change can then be restored with a new MAJOR version. Or,
    * If you decide that reverting will cause more harm for some reason, and you do not intend to revert, please let this be a part of your communication.

Don't be concerned with the MAJOR version getting too high, it is just a number. If you are concerned about people having to read about incompatible changes too often, you could find ways to avoid the breaking change or deprecate and group several breaking changes at a later point.

Tooling and Automation
======================

* A checklist item in a pull-request template can remind people of any manual steps.
* Some frontend libraries have been using an npm package called `semantic-release`_. The `semantic-release`_ package automates incrementing the version appropriately for each PR merge, based on a well formatted commit comment.

  * One important formatting detail is to include "BREAKING CHANGE" at the beginning of a line in the commit description to get a new major version.

* Other tools can be added here over time.

.. _semantic-release: https://github.com/semantic-release/semantic-release

References
==========

* `Semantic Versioning v2`_
* `OEP-44: Changelogs`_

.. _Semantic Versioning v2: https://semver.org/spec/v2.0.0.html
.. _`OEP-44: Changelogs`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.rst
