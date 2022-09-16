OEP-58: Translations Management
###############################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-58 <oep-0058-arch-translations-management>`
   * - Title
     - Translations Management
   * - Last Modified
     - 2022-08-10
   * - Authors
     - 
       * Carlos Muniz <cmuniz@tcril.org>
       * Feanil Patel <feanil@tcril.org>
       * Sarina Canelake <sarina@tcril.org>
   * - Arbiter
     - Ned Batchelder <ned@edx.org>
   * - Status
     - Under Review
   * - Type
     - Architecture Decision
   * - Created
     - 2022-08-08
   * - Review Period
     - TBD
..    * - Resolution
..      - 

.. contents::
  :local:
  :depth: 1

Context
*******

There are problems with the current method of managing translations via the
edx-transifex-bot. The edx-transifex-bot uses a soon to be deprecated (Nov 30, 2022)
version of the Transifex API, and requires admin rights on Transifex in order to
function. In addition, it is difficult to track why some PRs are merged by the bot, and
some are not, and where the bot is creating and merging PRs. Most recently, it was
discovered that the translations were not uploading properly but it has been impossible
to debug exactly why. In the week before the Nutmeg release, this was a significant pain
point.

Decision
********

To alleviate these issues, we will switch from using the edx-transifex-bot to the GitHub
Transifex App, a stable app provided by Transifex. Benefits of this change include being
easier to maintain and solving a lot of the pain points detailed below. As part of this
proposal, we suggest moving translations into their own repository, to make using the
GitHub Transifex app more streamlined and straightforward, and in order to make
organizing and using the up to date translations simpler.

Current State
*************

* Edx-transifex-bot is a potential security issue: The edx-transifex-bot requires admin
  rights on Transifex in order to function. Admin rights give access to private/sensitive
  information as well as the ability to permanently delete translation and configuration
  files. At some point, the login to the edx-transifex-bot user was lost, and without
  access to the scripts that the bot uses to function, this edx-transifex-bot is a
  security issue we cannot control or debug.
* Edx-transifex-bot is a black box: Login/scripts for edx-transifex-bot cannot be found
  on GitHub or on wiki/secrets pages, and it is difficult to observe what work it is
  supposed to do, or whether it is doing it correctly.
* The translations for the Open edX Maple Release were never uploaded to Transifex,
  because the automation handled by the edx-transifex-bot never uploaded it.
* The underlying library and Transifex API (V2) are being deprecated. This has led to
  insconsistent behavior by our tooling when we try to automatically manage translations.
  See `this pull request`_ for more details.
* We have a complex process for managing translations for the named releases. As a
  result, the black box nature of the transifex bot and the deprecation of the underlying
  tooling, this has become more laborious to keep running. Especially because there are
  few people with Admin rights to Transifex and knowledge of the Transifex API; this
  could become a reccuring problem with each Open edX release.

.. _this pull request: https://github.com/openedx/edx-platform/pull/30567

Rationale for migrating to the Transifex GitHub App
***************************************************

* This is an upgrade of a system we use regularly, but do not want to have to maintain
  regularly.
* Upgrading from a bot (machine user) to an app/workflow is recommended by GitHub and
  makes the translation process more open source. 
* The GitHub Transifex Integration App is developed and supported by Transifex
* The Github Transifex App is very simple to configure and has many options. We can set
  Transifex Projects to automatically upload/download translation files from a repository
  once the translations are reviewed and accepted.
* By using an app that is maintained by Transifex the organization, we reduce the
  maintenance burden and are more future proof of changes they might make since they
  maintain both the API and the GitHub App.

Rationale for consolidating translations files centrally
********************************************************

* Transifex only allows a one-to-one relationship between repositories and Transifex
  Projects. Organizing all of the translation files into one repository and one Transifex
  Project has a lower labor cost: projects are managed separately so we end up spending
  less time tracking translation progress, and debuging translation issues when all
  translation files are put in the same place.
* A repository that only contains text/binary files, and uses branches to separate
  translations related to Open edX releases can make all interactions with translations
  very quick and simple due to the ability to clone the branch of a specific release,
  with translations organized by repository name.

Proposed Implementation
***********************

Move Translation Files to a New Repo
====================================

Translation files (of types .mo and .po) currently exist amongst the code/documentation
they translate. We will move these translation files from being amongst the
code/documentation to their own repo. For example, a translation file for the openedx
repository edx-platform_ located at ``edx-platform/conf/locale/en/LC_MESSAGES/django.po``
would be moved to the new repository with the name openedx-translations_ located at
``openedx-translation/edx-platform/conf/locale/en/LC_MESSAGES/django.po``. For easier
reintegration, translation files will be kept in the same directory structure as the
code/documentation they translate.

.. _edx-platform: https://github.com/openedx/edx-platform
.. _openedx-translations: https://github.com/openedx/openedx-translation

Add GitHub Transifex App to openedx Organization
================================================

The Transifex GitHub app will need to be added to the openedx GitHub organization in
order to grant the app permissions to push/pull the translation files. Currently, we
manage the push/pull permissions for the edx-transifex-bot through a number of GitHub
user groups. The Transifex GitHub app once installed in an organization, is granted
permissions to push/pull on a repository basis, and by moving all the translation files
to a single repository we eliminate separate translations user groups.

Connect the New Translation Repository to Transifex
===================================================

The Transifex webapp accepts configuration files for each Transifex project. By
connecting the single repository containing all translation files, we only need to make a
single configuration file that allows the Transifex GitHub app to manage the translation
files. Based on the Translation Working Group's instruction on acceptable
translation/review percentages, we can set parameters that automatically push and pull
translation files.

Copy Transifex Memory and Combine Translators
==============================================

As a last step we will reorganize the openedx Transifex organization by combining
translators and reviewers across Transifex projects into the new project associated with
the new repository. In addition, we can save all the progress the Open edX translators
have accomplished by copying the Transifex Memory, the auto-translation feature that
allows for Projects with similar strings to be automatically translated, from the old
projects to this new one. Once older projects are made redundnant by the new project,
they will be deprecated. By moving all the translation files to the same repository we
can increase the reach of the Transifex Memory feature to help translate similar strings
across the entire code/documentation base.

Impacts
*******

Impact on Translators
=====================

As we approach the end of the translation upgrade process, we will need to tactically
move from multiple transifex projects to a single project.  This will require
coordination with our translators to ensure that moving forward they are providing
translations in the right place.

Impact on Site Operators
========================

Currently the translation files for any given service or library is stored at the same
place as the code, which has generally simplified the deployment story in the past.  With
this change, the translations files will move to their own repository.  As we deprecate
the old translations files, the relevant deployment tooling will need to be updated to
pull down the translations from the new repository as a part of the deployment process.
This will impact both the old Ansible based tooling as well as any new docker based
tooling.

Impact on Developers
====================

While it won’t directly impact the day-to-day workflow of developers, due to the same
reasons that we impact site operators (new translations location), we will have to update
development tools as well.

Locations
*********

Dumps of the translation/localization files from Transifex for the Open edX Release
project already exist in a repository with the name of openedx/openedx-i18n. A new
repository named openedx/openedx-translations will be similarly structured, but it will
contain the translation files for all repositories within openedx. The GitHub Transifex
app will be installed in the openedx organization. Similar to how the Build-Test-Release
Working Group creates a new branch for each new named release of edx-platform,
translation releases can also be kept in branches corresponding to edx-platform releases.

Rejected Alternatives
*********************

Rewriting the Current Tooling for the New API
=============================================

The source code for the edx-transifex-bot is missing. We could rewrite the current
tooling to try to solve the problems encountered in the last two Open edX releases and
upgrade to the new API, but this approach would require a full rewrite, potentially more
expensive than doing the rewrite in a way that Transifex more cleanly supports. It should
also be mentioned that GitHub discourages the use of bots and separate bot accounts; they
strongly recommend using GitHub Apps.

Making a Transifex Project for Each Repository
==============================================

As translation support is provided for more repos, the effort to maintain the
translations infrastructure increases. A Transifex Project houses the content to be
translated and needs to be created before any content can be added for translation.
Transifex Projects can only support 1 GitHub repository each and need to be maintained
separately. Maintaining a Transifex Project involves adjusting configurations, adding new
languages, assigning translators to projects, or any other miscellaneous irregular tasks
that would be time-consuming at a larger scale. If we add a Transifex Project, each
Transifex Project will need to be maintained separately, making debugging issues or
tracking the progress of each Transifex Project time-consuming.
