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

Context
=======

Many or all of the Open edX installable libraries, including both JavaScript and Python libraries, already use version numbers similar to those described by the `Semantic Versioning v2`_ specification. This OEP codifies this best practice, as well as providing additional guidance around proper versioning.

Decision
========

All Open edX installable libraries will generally follow the `Semantic Versioning v2`_ specification.

Versioned code should use a MAJOR.MINOR.PATCH format for versioning. Here is the most relevant part from the summary in the `Semantic Versioning v2`_ specification:

    Given a version number MAJOR.MINOR.PATCH, increment the:

    * *MAJOR* version when you make *incompatible API changes*,
    * *MINOR* version when you *add functionality in a backwards compatible manner*, and
    * *PATCH* version when you make *backwards compatible bug fixes*.

One of the most important rules when choosing whether to increment the MAJOR, MINOR, or PATCH version, is that backward incompatible changes always result in a new MAJOR version, and never a MINOR or PATCH version.

You must increment the MAJOR version for backward incompatible changes, even if you think it is unlikely to affect anyone. You can simply note in the changelog entry why it is backward incompatible and why you think it is unlikely to affect anyone.

If you find a backward incompatible change in a MINOR or PATCH version after publishing the package, please update the changelog and communicate the inconsistency. Additionally, you have the following options:

* A new MINOR or PATCH version can be immediately released to revert the change, and the change can be restored with a new MAJOR version. Or,
* If you decide that reverting will cause more harm for some reason, and you do not intend to revert, please let this be a part of your communication.

The reason it is important that only MAJOR versions include backward incompatible change is because when people upgrade dependencies, some people will only read changelogs for MAJOR versions because of the potential backward incompatibilities, and may not read notes for MINOR or PATCH upgrades.

Tooling and Automation
======================

The following provides information about related tooling, but is not necessarily part of the decision.

Frontend Libraries
------------------

All new frontend libraries have been using an npm package called `semantic-release`_.

The `semantic-release`_ package automates incrementing the version appropriately for each PR merge, based on a well formatted commit comment. See the `semantic-release`_ README for details. A pre-commit hook is typically run to ensure the commit message is appropriately formatted for `semantic-release`_.

* One important formatting detail is to include "BREAKING CHANGE" at the beginning of a line in the commit description to get a new major version.

.. _semantic-release: https://github.com/semantic-release/semantic-release

Python Libraries
----------------

Unlike with frontend libraries, most of the Open edX Python libraries require the version to be manually updated.

A repository's pull-request template is a great way to remind people to update the version when this must be done manually.

References
==========

* `Semantic Versioning v2`_
* `OEP-44: Changelogs`_

.. _Semantic Versioning v2: https://semver.org/spec/v2.0.0.html
.. _`OEP-44: Changelogs`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.rst
