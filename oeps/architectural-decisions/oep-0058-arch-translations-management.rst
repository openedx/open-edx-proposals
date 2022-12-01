OEP-58: Translations Management
###############################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-58 <oep-0058-arch-translations-management>`
   * - Title
     - Translations Management
   * - Last Modified
     - 2022-11-22
   * - Authors
     - 
       * Carlos Muniz <cmuniz@tcril.org>
       * Feanil Patel <feanil@tcril.org>
       * Sarina Canelake <sarina@tcril.org>
   * - Arbiter
     - Ned Batchelder <ned@edx.org>
   * - Status
     - Provisional
   * - Type
     - Architecture Decision
   * - Created
     - 2022-08-08
   * - Review Period
     - 2022-11-07 - 2022-11-21
   * - References
     - `Follow-up Work <https://openedx.atlassian.net/wiki/spaces/COMM/pages/3578494977/Translations+Management+Design+Implementation>`_

.. contents::
  :local:
  :depth: 1

Abstract
********

We will switch from using the edx-transifex-bot to the `Transifex GitHub App`_, a stable
app provided by Transifex. Benefits of this change include being easier to maintain and
solving a lot of the pain points detailed below. In addition, translation files will be
moved into their own repository. This will make using the `Transifex GitHub App`_ more
streamlined and straightforward, and facilitate the organization of translation files on
Transifex. A CLI tool will be developed to move translation files from the repository
that contains them to where they are needed for development or deployment.

Motivation
**********

The current method of managing and organizing translations files is overly complicated
and unavailable to the majority of the Open edX community. For example: the
edx-transifex-bot performs the automated upload of English translation source files to
Transifex and download of translation files to GitHub. It currently runs on legacy
infrastructure originally provided by a community member and is difficult to track why
some PRs are merged by the bot, and some are not, and where the bot is creating and
merging PRs. Most recently, it was discovered that the translations were not uploading
properly but it has been impossible for most members of the Open edX community to debug
exactly why. In the week before the Nutmeg release, this was a significant pain point.

Current State
=============

* edx-transifex-bot is a potential security issue: The edx-transifex-bot requires admin
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

Rationale for migrating to the `Transifex GitHub App`_
======================================================

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
* Transifex has a very robust notification system, and errors occurring there will notify
  the Transifex Admins and the Translation Working Group.

Rationale for consolidating translations files centrally
========================================================

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

Specification
*************

Move Translation Files to a New Repo
====================================

Translation files (of types .mo, .po, and .json) currently exist amongst the code and
documentation they translate. We will move these translation files from being amongst the
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
to the `openedx-translations`_ repository, the `Transifex GitHub App`_ will link a Transifex
project of a name such as "openedx-translations" to the `openedx-translations`_ repository.
A `Transifex GitHub Integration configuration file`_ naming the files that are to be
translated and the trigger that pulls translation files back into will be created in the
openedx/translations repository. This link will allow for the `Transifex GitHub App`_ to
automatically manage the push/pull of the translation files without the need for human
intervention.

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

A new CLI tool called `openedx-atlas`_ will be created to enable the placement of the
translation files kept in `openedx-translations`_ into locally cloned repositories for
development and containers containing the code translation files are formed from. This
tool will manage the placement of translation files through an editable atlas
configuration file (atlas.yml) kept in the repositories that have translation files kept
in openedx-translations. The atlas.yml file will support options that allow for the
concatenation, reorganization, and reformatting of translation files as they are copied
to their locations amongst the code. The atlas.yml file will also support selecting which
languages to be included in an Open edX deployment. The tool will have to be used/ran as
part of the setup of a repository, whether for development or deployment. The
`openedx-atlas`_ tool can also be run without configuration files through CLI parameters
that override atlas.yml. This tool is still in development, and while the language the
tool is written in may change, the commands and purpose will not change.

Impact
******

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
This will impact both the old Ansible based tooling as well as any new Docker based
tooling.

Impact on Developers
====================

While it won’t directly impact the day-to-day workflow of developers (unless you are
developing or testing with translation files), due to the same reasons that we impact
site operators (new translations location), we will have to update development tools as
well. In addition, we will create new instructions for developers on how to enable
translations for a new service/repo when it comes online. Information about the use of
the `openedx-atlas`_ CLI tool will also be added to READMEs and Makefiles as necessary.

Locations
=========

Dumps of the translation/localization files from Transifex for the Open edX Releases
already exist in the repository `openedx-i18n`_. A new repository named
`openedx-translations`_ will have a similar role, but it will contain the translation
files for all languages and for all repositories within the GitHub openedx organization.
This OEP will deprecate `openedx-i18n`_ since it will contain strings for all languages,
repositories, and will follow the regular minor/major release schedule.

The `Transifex GitHub App`_ will be installed in the openedx organization.
Similar to how the Build-Test-Release Working Group creates a new branch for each new
named release of edx-platform, translation releases will also be kept in branches
corresponding to edx-platform releases.

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
translations infrastructure increases. A Transifex Project contains the content to be
translated and needs to be created before any content can be added for translation.
Transifex Projects can only support one GitHub repository each and need to be maintained
separately. Maintaining a Transifex Project involves adjusting configuration files,
adding new languages, assigning translators to projects, as well as any other
miscellaneous irregular tasks that would be time-consuming at a larger scale. If we add a
Transifex Project, each Transifex Project will need to be maintained separately, making
debugging issues or tracking the progress of each Transifex Project time-consuming. In
addition, the Transifex editor does not support editing strings across multiple projects
making it extremely time consuming for users to translate strings from many projects.

.. _ecommerce-scripts: https://github.com/openedx/ecommerce-scripts/tree/master/transifex
.. _edx-platform: https://github.com/openedx/edx-platform
.. _openedx-atlas: https://github.com/openedx/openedx-atlas
.. _openedx-i18n: https://github.com/openedx/openedx-i18n
.. _openedx-translations: https://github.com/openedx/openedx-translations
.. _this pull request: https://github.com/openedx/edx-platform/pull/30567
.. _Transifex GitHub App: https://github.com/apps/transifex-integration
.. _Transifex GitHub Integration configuration file: https://docs.transifex.com/transifex-github-integrations/github-tx-ui#linking-a-specific-project-with-a-github-repository
