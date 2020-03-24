##################
OEP-44: Changelogs
##################

.. list-table::

   * - OEP
     - :doc:`OEP-0044 </oeps/oep-0044>`
   * - Title
     - Changelogs
   * - Last Modified
     - 2020-03-19
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

There are a variety of positions taken in Open edX by individual developers and for each repository regarding how and if and when and why to include changelogs, and how to manage software versions. This OEP will provide best practices for managing both changelogs and versions.

Background
==========

A Changelog Definition
----------------------

A very useful reference is `keepachangelog.com`_, although this OEP does depart from this in some significant ways.  The following questions and answers are copied directly from `keepachangelog.com`_

What is a changelog?
~~~~~~~~~~~~~~~~~~~~

A changelog is a file which contains a curated, chronologically ordered list of notable changes for each version of a project.

Why keep a changelog?
~~~~~~~~~~~~~~~~~~~~~

To make it easier for users and contributors to see precisely what notable changes have been made between each release (or version) of the project.

Who needs a changelog?
~~~~~~~~~~~~~~~~~~~~~~

People do. Whether consumers or developers, the end users of software are human beings who care about what's in the software. When the software changes, people want to know why and how.

Types of Repositories
---------------------

For the purposes of this OEP, we will refer to two types of repositories.

**Library Repository**: A library is a repository containing code that is versioned. These libraries often produce packages that are published to PyPI (for Python) or npm (for JavaScript).

**Application or Non-Library Repository**: Any repository that does not contain versioned code will be referred to as an application or non-library respository.

Decisions
=========

All Open edX repositories should contain a CHANGELOG.rst. There are a variety of acceptable formats, depending on the type of repository and other factors, as discussed below.

Changelogs and Changelog Entries
--------------------------------

Here are some recommendations for formatting changelogs and changelog entries, which have been taken from `keepachangelog.com`_.

Guiding Principles
~~~~~~~~~~~~~~~~~~

* Changelogs are for humans, not machines.
* There should be an entry for every single version [for libraries].
* The same types of changes should be grouped.
* Versions and sections should be linkable.
* The latest version comes first.
* The release date of each version is displayed.
* Mention whether you follow Semantic Versioning.

Types of Changes
~~~~~~~~~~~~~~~~

* **Added** for new features.
* **Changed** for changes in existing functionality.
* **Deprecated** for soon-to-be removed features.
* **Removed** for now removed features.
* **Fixed** for any bug fixes.

These make great section headers, but similar language could also be used in messages to clarify the type of change.  For commits, you may want to use "adds" in place of "added", etc.

The recommendation also includes "**Security** in case of vulnerabilities", but that is being called out separately because the Security Group needs to manage the timing of adding any such designation to any public changelog.

Other Recommendations
~~~~~~~~~~~~~~~~~~~~~

* Format dates as YYYY-MM-DD. As an example, 2020-03-21.
* All notable changes must be included in your changelog. Deprecations, removals, and breaking changes are always notable changes.
* It is both acceptable and appropriate to clean up changelog entries at any time to add clarifications or customize messaging for the audience of the changelog when needed.

Example Changelog
~~~~~~~~~~~~~~~~~

Here is an example CHANGELOG.rst for `Application and Non-Library Repositories`_, which have no versions.

::

  Changelog
  ===========

  All notable changes to this project will be documented in this file.

  The format is based on recommendations in `OEP-44`_.

  .. _OEP-44: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.html

  2020-03-21
  ----------

  Optional summary text for this date's entry.

  Removed
  ~~~~~~~

  * Removed deprecated function. See DEPR-XXX for details.
  * Removed deprecated feature YYY.

    * Optional additional text about this change, including breaking change or any other details useful to the reader of the changelog. For example, you might also provide details of an alternative to the deprecated feature (if there is one), or a link to these details.

  2020-03-21
  ----------

  Added
  ~~~~~

  * Feature x. See announcement here: https://xxx

Notable Differences
~~~~~~~~~~~~~~~~~~~

The following are notable differences between the recommendations of this OEP and the `keepachangelog.com`_ recommendations:

* We use GitHub Releases in certain cases, as you'll see below, for developer efficiency purposes.
* We sometimes use automation that uses git commit messages for changelog messages. As noted above, it is acceptable and appropriate to clean these up as needed.

Library Repositories
--------------------

All Open edX library repositories should contain a CHANGELOG.rst. These libraries should all adhere to `Semantic Versioning`_.

Semantic Versioning
~~~~~~~~~~~~~~~~~~~

All versioned code should use a MAJOR.MINOR.PATCH format for versioning, as defined by the `Semantic Versioning v2`_ specification. Here is a copy of the most relevant part of the summary:

Given a version number MAJOR.MINOR.PATCH, increment the:

* *MAJOR* version when you make *incompatible API changes*,
* *MINOR* version when you *add functionality in a backwards compatible manner*, and
* *PATCH* version when you make *backwards compatible bug fixes*.

Every published package should have a corresponding changelog entry for the new version.

One of the most important rules when choosing whether to increment the MAJOR, MINOR, or PATCH version, is that backward incompatible changes always result in a new MAJOR version, and never a MINOR or PATCH version.

You must increment the MAJOR version for backward incompatible changes, even if you think it is unlikely to affect anyone. You can simply note in the changelog entry why it is backward incompatible and why you think it is unlikely to affect anyone.

If you find a backward incompatible change in a MINOR or PATCH version after publishing the package, please update the changelog and communicate the inconsistency. Additionally, you have the following options:

* A new MINOR or PATCH version can be immediately released to revert the change, and the change can be restored with a new MAJOR version. Or,
* If you decide that reverting will cause more harm for some reason, and you do not intend to revert, please let this be a part of your communication.

The reason it is important that only MAJOR versions include backward incompatible change is because when people upgrade dependencies, some will only read changelogs for MAJOR versions because of the potential backward incompatibilities, and may not read notes for MINOR or PATCH upgrades.

Frontend Libraries
~~~~~~~~~~~~~~~~~~

Like all Open edX repositories, frontend libraries should also include a CHANGELOG.rst. However, as you'll see below, this file will simply point to the GitHub Releases. Here is an example CHANGELOG.rst::

  Changelog
  =========

  All notable changes to this project will be documented as `GitHub Releases`_.

  This project adheres to `Semantic Versioning`_.

  .. _GitHub Releases: https://github.com/edx/frontend-build/releases
  .. _Semantic Versioning: https://semver.org/spec/v2.0.0.html


All new frontend libraries have been using an npm package called `semantic-release`_.

The `semantic-release`_ package automates the following:

* Incrementing the version appropriately for each PR merge, based on a well formatted commit comment. See the `semantic-release`_ README for details. A pre-commit hook is typically run to ensure the commit message is appropriately formatted for `semantic-release`_.

  * One important formatting detail is to include "BREAKING CHANGE" at the beginning of a line in the commit description to get a new major version.

* Publishing the changelog message as a GitHub Release in the repository, using the commit message. Here is an example `GitHub Release changelog for edx/frontend-build`_.

As noted above, please update the changelogs as necessary to add clarifications or customize the messaging for the audience of the changelog, especially because these messages originally come from commit messages, which may have been written for a different audience.

.. _semantic-release: https://github.com/semantic-release/semantic-release
.. _GitHub Release changelog for edx/frontend-build: https://github.com/edx/frontend-build/releases

Python Libraries
~~~~~~~~~~~~~~~~

Like all Open edX repositories, Python libraries should also include a CHANGELOG.rst. This file may also simply point to the GitHub Releases. Here is an example CHANGELOG.rst::

  Changelog
  =========

  All notable changes to this project will be documented as `GitHub Releases`_.

  This project adheres to `Semantic Versioning`_.

  .. _GitHub Releases: https://github.com/edx/frontend-build/releases
  .. _Semantic Versioning: https://semver.org/spec/v2.0.0.html

Many Python libraries publish packages to PyPI when a version is tagged. Publishing a GitHub Release both tags the version and allows one to write an appropriate changelog message. This is the recommended process at this time.

Every published package should have a corresponding changelog entry for the new version.

Application and Non-Library Repositories
----------------------------------------

When it comes to changelogs, the main difference between application and non-library repositories, and the eariler recommendations for library repositories, is that non-library code is not versioned.

Since this code has no versions, the recommendations differ in the following ways:

* The changelog entries will live in the CHANGELOG.rst file, following the recommendations detailed in `Changelogs and Changelog Entries`_.
* Changelog entries should start with a date, rather than a date and version, because these repositories are not versioned.

Tooling and Automation
----------------------

Minimally, a pull-request template is a great way to remind people of any manual step required around changelogs.

As noted above, `Frontend Libraries`_ already use automation to write changelog entries to GitHub Releases.

For other repositories, more exploration is required around tooling to help automate.  This could include:

* Tools that use well formatted commit messages to get at least an initial pass at changelog messages.
* Tools that put changelog messages into separate temporary files to be processed and avoid merge conflicts.
* Any other tools required to make it simple to document appropriate changelog messages as early as possible.

Consequences
============

As noted earlier, `keepachangelog.com`_ recommends against using GitHub Releases. We have found, at least for `Frontend Libraries`_, that increased developer efficiency is worth the non-portability and slight variance in message formatting.

Many repositories do not have a CHANGELOG.rst, and would require one to comply with this OEP. In most cases, this would simply be a boiler-plate CHANGELOG.rst that points to the GitHub Releases url for that repository.

In some cases, like `edx/configuration CHANGELOG`_, adding dates would add clarity to the order of the log and would stop people from adding to the bottom, which sometimes happens. This file should also be changed to rST format, in keeping with OEP-19.

.. _edx/configuration CHANGELOG: https://github.com/edx/configuration/blob/master/CHANGELOG.md

Rejected Alternatives
=====================

For `Application and Non-Library Repositories`_, the only real alternatives to a CHANGELOG.rst are:

#. Keeping changelog details in Confluence or some other location, or
#. Not keeping any changelog at all.

The first of these alternatives goes against our developer documentation recommendations, by not co-locating documentation with its code. The second alternative makes it impossible to learn about important changes without a great deal of hunting through commit comments.

For `Python Libraries`_, an alternative to using GitHub Releases would be to add individual changelog entries directly into the CHANGELOG.rst. Given that `Frontend Libraries`_ are already using GitHub Releases, and many Python libraries already use the GitHub Releases, it seems simpler to be consistent. Additionally, repositories that have used both GitHub Releases and entries in the CHANGELOG.rst tend to have inconsistent usage, where various entries get dropped from one or the other. It is also simpler to point the CHANGELOG.rst to GitHub Releases than to point GitHub Releases to the CHANGELOG.rst or to stop people from adding GitHub Releases.

References
==========

* `keepachangelog.com`_
* `Semantic Versioning v2`_

.. _keepachangelog.com: https://keepachangelog.com/en/1.0.0/
.. _Semantic Versioning v2: https://semver.org/spec/v2.0.0.html
