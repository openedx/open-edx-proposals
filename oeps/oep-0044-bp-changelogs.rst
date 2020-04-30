##################
OEP-44: Changelogs
##################

.. list-table::

   * - OEP
     - :doc:`OEP-0044 </oeps/oep-0044>`
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

There are a variety of positions taken in Open edX by individual developers and for each repository regarding how and if and when and why to include changelogs. This OEP will provide best practices for managing changelogs.

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

Here are some recommendations for formatting changelogs and changelog entries, which have been adapted from `keepachangelog.com`_.

Guiding Principles
~~~~~~~~~~~~~~~~~~

* Changelogs are for humans, not machines.
* There should be an entry for every single version [for libraries].
* The type of change should be clear.
* Versions and sections should be linkable.
* The latest version comes first.
* The release date of each version is displayed.

Types of Changes
~~~~~~~~~~~~~~~~

* **Added** for new features.
* **Changed** for changes in existing functionality.
* **Deprecated** for soon-to-be removed features.
* **Removed** for now removed features.
* **Fixed** for any bug fixes.

You can use these as the initial word in your changelog entry for consistency. Note: for commit messages, you may want to use "adds" in place of "added", etc.

The original recommendation also includes "**Security** in case of vulnerabilities", but that is being called out separately because the Security Group will manage the timing of adding any such designation to any public changelog.

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
  The format is based on recommendations in `OEP-44: Changelogs`_.

  .. _`OEP-44: Changelogs`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.html

  2020-03-21
  ----------

  Optional summary text for this date's entry.

  * Removed deprecated function XXX. See DEPR-XXX for details.
  * Removed deprecated feature YYY.

    * Optional additional text about this change, including breaking change or any other details useful to the reader of the changelog. For example, you might also provide details of an alternative to the deprecated feature (if there is one), or a link to these details.

  2020-02-20
  ----------

  * Added feature x. See announcement here: https://xxx
  * Deprecated function XXX. See DEPR-XXX for details.

Notable Differences
~~~~~~~~~~~~~~~~~~~

The following are notable differences between the recommendations of this OEP and the `keepachangelog.com`_ recommendations:

* We use GitHub Releases in certain cases, as you'll see below, for developer efficiency purposes.
* We sometimes use automation that creates initial changelog messages from git commit messages. As noted above, it is acceptable and appropriate to clean these up as needed.
* Although it would be fine for a changelog to group entries by the type of change, it is also fine not to, as long as the change type is clear in the message.

Library Repositories
--------------------

All Open edX library repositories should contain a CHANGELOG.rst.

Frontend Libraries
~~~~~~~~~~~~~~~~~~

Like all Open edX repositories, frontend libraries should also include a CHANGELOG.rst. However, as you'll see below, this file will simply point to the GitHub Releases. Here is an example CHANGELOG.rst::

  Changelog
  =========

  All notable changes to this project will be documented as `GitHub Releases`_.
  The release notes format should include information recommended in `OEP-44: Changelogs`_.
  This project adheres to `Semantic Versioning`_.

  .. _GitHub Releases: https://github.com/edx/frontend-build/releases
  .. _`OEP-44: Changelogs`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.html
  .. _Semantic Versioning: https://semver.org/spec/v2.0.0.html

Every package published to npm should have a corresponding changelog entry for the new version.

All new frontend libraries have been using an npm package called `semantic-release`_, which automates publishing the changelog message as a GitHub Release in the repository, using the commit message. Here is an example `GitHub Release changelog for edx/frontend-build`_. The versions are automatically tagged using semantic versioning, as detailed in `OEP-47: Semantic Versioning`_.

Please update the changelogs as necessary to add clarifications or customize the messaging for the audience of the changelog, especially because these messages originally come from commit messages, which may have been written for a different audience.

.. _semantic-release: https://github.com/semantic-release/semantic-release
.. _GitHub Release changelog for edx/frontend-build: https://github.com/edx/frontend-build/releases

Python Libraries
~~~~~~~~~~~~~~~~

Like all Open edX repositories, Python libraries should also include a CHANGELOG.rst. This file may simply point to the GitHub Releases. Here is an example CHANGELOG.rst::

  Changelog
  =========

  All notable changes to this project will be documented as `GitHub Releases`_.
  The release notes format should include information recommended in `OEP-44: Changelogs`_.
  This project adheres to `Semantic Versioning`_.

  .. _GitHub Releases: https://github.com/edx/frontend-build/releases
  .. _`OEP-44: Changelogs`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.html
  .. _Semantic Versioning: https://semver.org/spec/v2.0.0.html

Every package published to PyPI should have a corresponding changelog entry for the new version.

Many Python libraries publish packages to PyPI when a version is tagged. Publishing a GitHub Release both tags the version and allows one to write an appropriate changelog message. This is the recommended process at this time to avoid issues where the CHANGELOG.rst and GitHub Releases are not in sync.

For a given Python Library repository, if a decision is made to add changelog entries to the CHANGELOG.rst, you **must** provide a way to ensure the CHANGELOG.rst and GitHub Releases are not out of sync. At this time, we do not have tooling for this.

Application and Non-Library Repositories
----------------------------------------

Unlike with libraries, application and non-library repositories contain code or files that are not versioned. Examples of these respositories include edx-platform and our other IDAs, like ecommerce, discovery, etc.

Since this code has no versions, the recommendations are as follows:

* The changelog entries will live in the CHANGELOG.rst file, following the recommendations detailed in `Changelogs and Changelog Entries`_.
* Changelog entries should start with a date, rather than a date and version, because these repositories are not versioned.

Tooling and Automation
----------------------

Minimally, a pull-request template is a great way to remind people of any manual step required around changelogs.

As noted above, `Frontend Libraries`_ already use automation to write changelog entries to GitHub Releases.

More exploration would be required around tooling to help automate and provide any new recommendations.

Consequences
============

As noted earlier, `keepachangelog.com`_ recommends against using GitHub Releases. We have found, at least for `Frontend Libraries`_, that increased developer efficiency is worth the non-portability and slight variance in message formatting. As noted above, entries can and should be updated when the commit message is not geared toward the same audience.

Many repositories do not have a CHANGELOG.rst, and would require one to comply with this OEP. In most cases, this would simply be a boiler-plate CHANGELOG.rst that points to the GitHub Releases url for that repository.

In some cases, like `edx/configuration CHANGELOG`_, adding dates would add clarity to the order of the log and would stop people from adding to the bottom, which sometimes happens. This file should also be changed to rST format, in keeping with OEP-19.

.. _edx/configuration CHANGELOG: https://github.com/edx/configuration/blob/master/CHANGELOG.md

Rejected Alternatives
=====================

For `Application and Non-Library Repositories`_, the only real alternatives to a CHANGELOG.rst are:

#. Keeping changelog details in Confluence or some other location, or
#. Not keeping any changelog at all.

The first of these alternatives goes against our developer documentation recommendations, by not co-locating documentation with its code. The second alternative makes it impossible to learn about important changes without a great deal of hunting through commit comments.

References
==========

* `keepachangelog.com`_
* `OEP-47: Semantic Versioning`_

.. _keepachangelog.com: https://keepachangelog.com/en/1.0.0/
.. _`OEP-47: Semantic Versioning`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0047-bp-semantic-versioning.rst
