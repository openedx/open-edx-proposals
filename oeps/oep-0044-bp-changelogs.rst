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

Summary
=======

.. note::

    This OEP is on hold at this time while some of these recommendations are first trialed in some repositories.

This OEP contains recommendations rather than requirements for changelogs.

* The recommendation is for each repository to have a CHANGELOG.rst.
* To learn about maintaining a changelog, see `Formatting Changelogs`_, `Example Changelog`_, and `Commit Message vs Changelog Entry`_.
* For more general information, see the `What and Why of Changelogs`_.

Context
=======

Changelogs are handled differently in many Open edX repositories, and this OEP provides guidance if you are wondering which method to choose.

What and Why of Changelogs
==========================

A useful reference is `keepachangelog.com`_, although this OEP is less strict in its recommendations.

The following questions and answers are copied from `keepachangelog.com`_

* **What is a changelog?**: A changelog is a file which contains a curated, chronologically ordered list of notable changes for each version of a project.
* **Why keep a changelog?**: To make it easier for users and contributors to see precisely what notable changes have been made between each release (or version) of the project.
* **Who needs a changelog?**: People do. Whether consumers or developers, the end users of software are human beings who care about what's in the software. When the software changes, people want to know why and how.

Recommendations
===============

All Open edX repositories should contain a CHANGELOG.rst.

The recommendations for formatting changelogs have been adapted from `keepachangelog.com`_.

Formatting Changelogs
~~~~~~~~~~~~~~~~~~~~~

* Changelogs are for humans, not machines.
* There should be an entry for every version [for versioned code].
* The type of each change should be made clear. See `Types of Changes`_.
* Versions and sections should be linkable.
* The latest version comes first.
* The release date of each version is displayed.
* Format dates as YYYY-MM-DD. For example, 2020-03-21.
* Include all notable changes. Deprecations, removals, and breaking changes are always notable changes.
* It is both acceptable and appropriate to clean up or fix changelog entries at any time.

Types of Changes
~~~~~~~~~~~~~~~~

These are useful words to start your changelog entries.

* **Add** for new features.
* **Change** for changes in existing functionality.
* **Deprecate** for soon-to-be removed features.
* **Remove** for now removed features, usually prefixed with **BREAKING CHANGE:**.
* **Fix** for any bug fixes.

Include **BREAKING CHANGE** for all backward incompatible changes.
The Security Working Group may want to manage timing when using: **Security** in case of vulnerabilities.

Example Changelog
~~~~~~~~~~~~~~~~~

Here is an example CHANGELOG.rst for a repository that contains unversioned code.

::

  Changelog
  ===========

  All notable changes to this project will be documented in this file.
  The format is based on recommendations in `OEP-44: Changelogs`_.

  .. _`OEP-44: Changelogs`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0044-bp-changelogs.html

  2020-03-21
  ----------

  Optional summary text for this date's entry.

  * **BREAKING CHANGE**: Remove deprecated function XXX. See DEPR-XXX for details.

    * Optionally use sub-bullet for additional text or links about this change.

  * **BREAKING CHANGE**: Remove deprecated feature YYY.


  2020-03-14
  ----------

  * Added feature x. See announcement here: https://xxx
  * Deprecated function XXX. See DEPR-XXX for details.

For a repository with versioned code, use the version in the headers like::

  [1.0.0] - 2020-03-21
  --------------------

* You may include an optional section at the top to collect changes that aren't yet released, although we typically release every change

::

  [Unreleased]
  ------------

Commit Message vs Changelog Entry
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Since changelog messages are for developers and consumers of your code, good changelog messages will often not match commit messages. Here are some example updated messages.

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - Commit Message
     - Changelog Entry
   * - deps: update dependency css-loader to v3.5.1
     - No functional change

       Note: This example changelog entry assumes there were no other changes for this version.
   * - Fix SyntaxError in UserLogout class
     - Fix 500 error during logout
   * - **BREAKING CHANGE** Remove the dry_run parameter in the public foobarize API method. This parameter is deprecated in favour of the no_apply parameter. See docs for details.
     - Rename dry_run parameter
   * - Add a set_foobarizer method to Foo's public API. This is particularly useful for developers trying to foobarize their users. See docs for details.
     - Add set_foobarizer method to api.Foo

Github Releases
~~~~~~~~~~~~~~~

* Github Releases should not replace the CHANGELOG.rst.
* If used, Github Releases should stay in sync with the CHANGELOG.rst.

  * It may be simplest to avoid them altogether, or
  * You could publish each release with a link to the relevant version in the CHANGELOG.rst.

Tooling and Automation
======================

* Use a pull-request template to remind people of any manual steps required.
* Many Open edX frontend libraries are using `semantic-release`_ to automate the release process.

  * Uses specially formatted commit messages. This may provide a good start, but see `Commit Message vs Changelog Entry`_ for recommendations around language.
  * According to docs, `semantic-release`_ could be configured to produce Changelogs over Github Releases.

* Other automation requires further research and can later be included here.

.. _semantic-release: https://github.com/semantic-release/semantic-release

Consequences
============

Since these are only recommendations, strictly speaking, there are no consequences.

If you want to follow the best practice, update your repository to follow this guidance.

Rejected Alternatives
=====================

For repositories without versioned code, like edx-platform and other IDAs, the only real alternatives to a CHANGELOG.rst are:

#. Keeping changelog details in Confluence or some other location, or
#. Not keeping any changelog at all.

The first of these alternatives goes against our developer documentation recommendations, by not co-locating documentation with its code. The second alternative makes it impossible to learn about important changes without a great deal of hunting through commit comments.

Github Releases is a proprietary tool with the following issues:

* It doesn't provide a single file view of changes, which is often helpful.
* It doesn't allow the changelog entries to be reviewed.
* It isn't available offline from within the codebase.

References
==========

* `keepachangelog.com`_
* `OEP-47: Semantic Versioning`_

.. _keepachangelog.com: https://keepachangelog.com/en/1.0.0/
.. _`OEP-47: Semantic Versioning`: https://open-edx-proposals.readthedocs.io/en/latest/oep-0047-bp-semantic-versioning.rst
