OEP-58: Translations Management
###############################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-58 <oep-0058-arch-translations-management>`
   * - Title
     - Translations Management
   * - Last Modified
     - 2022-11-07
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
     - 2022-11-07 - 2022-11-21
..    * - Resolution
..      - 

.. contents::
  :local:
  :depth: 1

Context
*******

The current method of managing and organizing translations files is overly complicated
and unavailable to the majority of the Open edX community. For example: the
edx-transifex-bot performs the automated upload of english translation source files to
Transifex and download of translation files to GitHub. It currently runs on legacy
infrastructure originally provided by a community member and is difficult to track why
some PRs are merged by the bot, and some are not, and where the bot is creating and
merging PRs. Most recently, it was discovered that the translations were not uploading
properly but it has been impossible for most members of the Open edX community to debug
exactly why. In the week before the Nutmeg release, this was a significant pain point.

Decision
********

To alleviate these issues, we will switch from using the edx-transifex-bot to the
`Transifex GitHub App`_, a stable app provided by Transifex. Benefits of this change
include being easier to maintain and solving a lot of the pain points detailed below. As
part of this proposal, we suggest moving translations into their own repository, to make
using the `Transifex GitHub App`_ more streamlined and straightforward, and in order to
make organizing and using the up to date translations simpler.

Current State
*************

* Edx-transifex-bot is a potential security issue: The edx-transifex-bot requires admin
  rights on Transifex in order to function. Admin rights give access to private/sensitive
  information as well as the ability to permanently delete translation and configuration
  files. At some point, the login to the edx-transifex-bot user was lost, and without
  access to infrastructure that the bot uses to function, this edx-transifex-bot is a
  security issue most of the Open edX community cannot control or debug.
* edx-transifex-bot is a black box for most of the community: the code for the
  edx-transifex-bot is in the `ecommerce-scripts`_ repository but it is impossible for
  most of the community to observe the work it is doing, or whether it is doing it
  correctly. In addition, there is no documentation for these important scripts.
* The translations for the Open edX Maple Release were never uploaded to Transifex,
  because the automation handled by the edx-transifex-bot never uploaded it.
* The underlying transifex-client library and Transifex API (V2) are being deprecated on
  November 30th, 2022. Prior to removing the transifex-client as a dependency, this led
  to inconsistent behavior by our tooling when we try to automatically manage
  translations. See `this pull request`_ for more details.
* We have a complex process for managing translations for the named releases. As a
  result, the black box nature of the edx-transifex-bot and the unclear state of the
  underlying tooling, this has become more laborious to keep running. Especially because
  there are few people with Admin rights to Transifex and knowledge of the Transifex API;
  this could become a recurring problem with each Open edX release.

.. _ecommerce-scripts: https://github.com/openedx/ecommerce-scripts/tree/master/transifex
.. _this pull request: https://github.com/openedx/edx-platform/pull/30567

Rationale for migrating to the `Transifex GitHub App`_
******************************************************

* This is an upgrade of a system we use regularly, but do not want to have to maintain
  regularly.
* Upgrading from a bot (machine user) to an app/workflow is recommended by GitHub and
  makes the translation process more open source. 
* The `Transifex GitHub App`_ is developed and supported by Transifex
* The `Transifex GitHub App`_ is very simple to configure and has many options. We can
  set Transifex Projects to automatically upload/download translation files from a
  repository once the translations are reviewed and accepted.
* By using an app that is maintained by Transifex the organization, we reduce the
  maintenance burden and are more future proof of changes they might make since they
  maintain both the API and the `Transifex GitHub App`_.

Rationale for consolidating translations files centrally
********************************************************

* Transifex only allows a one-to-one relationship between repositories and Transifex
  Projects. Organizing all of the translation files into one repository and one Transifex
  Project has a lower labor cost: projects are managed separately so we end up spending
  less time tracking translation progress, and debugging translation issues when all
  translation files are put in the same place. By decreasing the number of projects we
  need to maintain, we can add more content like the MFE translations.
* A repository that only contains text/binary files, and uses branches to separate
  translations related to Open edX releases can make all interactions with translations
  very quick and simple due to the ability to clone and sparse-checkout the branch of a
  specific release and the directory (repository name) with translation files.

Proposed Implementation
***********************

Move Translation Files to a New Repo
====================================

Translation files (of types .mo and .po) currently exist amongst the code/documentation
they translate. We will move these translation files from being amongst the
code/documentation to their own repo. For example, a translation file for the openedx
repository `edx-platform`_ located at
``edx-platform/conf/locale/en/LC_MESSAGES/django.po`` would be moved to the new
repository with the name openedx-translations_ located at
``openedx-translation/edx-platform/conf/locale/en/LC_MESSAGES/django.po``. For easier
reintegration, translation files will be kept in the same directory structure as the
code/documentation they translate.

Repositories that generate translation files will have their translation files generated
and committed via a pull request to the openedx-translation repository via a GitHub
workflow. Once the translation files from edx-platform and other repositories are moved
to the openedx-translations repository, the `Transifex GitHub App`_ will link a Transifex
project of a name such as "openedx-translations" to the openedx-translations repository.
A `Transifex GitHub Integration configuration file`_ naming the files that are to be
translated and the trigger that pulls translation files back into will be created in the
openedx/translations repository. This link will allow for the `Transifex GitHub App`_ to
automatically manage the push/pull of the translation files without the need for human
intervention.

.. _edx-platform: https://github.com/openedx/edx-platform
.. _openedx-translations: https://github.com/openedx/openedx-translation

Add `Transifex GitHub App`_ to openedx Organization
===================================================

The `Transifex GitHub App`_ will need to be added to the openedx GitHub organization in
order to grant the app permissions to push/pull the translation files. Currently, we
manage the push/pull permissions for the edx-transifex-bot through a number of GitHub
user groups. The `Transifex GitHub App`_ once installed in an organization, is granted
permissions to push/pull on a repository basis, and by moving all the translation files
to a single repository we eliminate separate translations user groups.

Connect the New Translation Repository to Transifex
===================================================

The Transifex web-app accepts a `Transifex GitHub Integration configuration file`_ for
each Transifex project. By connecting the single repository containing all translation
files, we only need to make a single `Transifex GitHub Integration configuration file`_
that allows the `Transifex GitHub App`_ to manage the translation files. Based on the
Translation Working Group's instruction on acceptable translation/review percentages, we
can set parameters that automatically push and pull translation files.

Copy Transifex's Translation Memory and Combine Translators
===========================================================

As a last step we will reorganize the openedx Transifex organization by combining
translators and reviewers across Transifex projects into the new project associated with
the new repository. In addition, we can save all the progress the Open edX translators
have accomplished by copying the Transifex's Translation Memory, the auto-translation
feature that allows for Projects with similar strings to be automatically translated,
from the old projects to this new one. Once older projects are made redundant by the new
project, they will be deprecated. By moving all the translation files to the same
repository we can increase the reach of the Transifex's Translation Memory feature to
help translate similar strings across the entire code/documentation base.

Get Translations Back for Deployment/Development
================================================

A new python library, called openedx-atlas, will be created. This will enable the
placement of the translation files kept in openedx-translations into locally cloned
repositories for development and containers containing the code translation files are
formed from. This tool will manage the placement of translation files through an editable
atlas configuration file (atlas.yml) kept in the repositories that have
translation files kept in openedx-translations. The atlas.yml file will support
options that allow for the concatenation, reorganization, and reformatting of translation
files as they are copied to their locations amongst the code. The atlas.yml file
will also support selecting which languages to be included in an Open edX deployment. The
tool will have to be used/ran as part of the setup of a repository, whether for
development or deployment.

Impacts
*******

Impact on Translators
=====================

As we approach the end of the translation upgrade process, we will need to tactically
move from multiple Transifex projects to a single project. This will require coordination
with our translators to ensure that moving forward they are providing translations in the
right place.

Impact on Site Operators
========================

Currently the translation files for any given service or library is stored at the same
place as the code, which has generally simplified the deployment story in the past. With
this change, the translations files will move to their own repository. As we deprecate
the old translations files, the relevant deployment tooling will need to be updated to
pull down the translations from the new repository as a part of the deployment process.
This will impact both the old Ansible based tooling as well as any new docker based
tooling.

Impact on Developers
====================

While it won’t directly impact the day-to-day workflow of developers (unless you are
developing or testing with translation files), due to the same reasons that we impact
site operators (new translations location), we will have to update development tools as
well. In addition, we will create new instructions for developers on how to enable
translations for a new service/repo when it comes online.

Locations
*********

Dumps of the translation/localization files from Transifex for the Open edX Release
project already exist in a repository with the name of openedx/openedx-i18n. A new
repository named openedx/openedx-translations will be similarly structured, but it will
contain the translation files for all repositories within openedx. The
`Transifex GitHub App`_ will be installed in the openedx organization. Similar to how the
Build-Test-Release Working Group creates a new branch for each new named release of
edx-platform, translation releases will also be kept in branches corresponding to
edx-platform releases.

Rejected Alternatives
*********************

Rewriting the Current Tooling for the New API
=============================================

The source code for the edx-transifex-bot can be found in `ecommerce-scripts`_. We could
rewrite the current tooling to try to solve the problems encountered in the last two Open
edX releases and upgrade to the new API, but this approach is a patch-up job that will
not address several other issues mentioned and would have to be undertaken by the
community member with exclusive access to the legacy infrastructure currently running the
edx-transifex-bot.

Making a Transifex Project for Each Repository
==============================================

As translation support is provided for more repos, the effort to maintain the
translations infrastructure increases. A Transifex Project houses the content to be
translated and needs to be created before any content can be added for translation.
Transifex Projects can only support one GitHub repository each and need to be maintained
separately. Maintaining a Transifex Project involves adjusting configuration files,
adding new languages, assigning translators to projects, or any other miscellaneous
irregular tasks that would be time-consuming at a larger scale. If we add a Transifex
Project, each Transifex Project will need to be maintained separately, making debugging
issues or tracking the progress of each Transifex Project time-consuming.

.. _Transifex GitHub App: https://github.com/apps/transifex-integration
.. _Transifex GitHub Integration configuration file: https://docs.transifex.com/transifex-github-integrations/github-tx-ui#linking-a-specific-project-with-a-github-repository
