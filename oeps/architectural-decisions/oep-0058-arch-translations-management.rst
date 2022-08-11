OEP-58: Translations Management
###############################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-58 <oep-0058-proc-translations-management>`
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
     - Process
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
easier to maintain and solving a lot of the pain points. As part of this proposal, we
suggest moving translations into their own repository, to make using  the GitHub
Transifex app more streamlined and straightforward, and in order to make organizing and
using the up to date translations simpler.

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
* The translations for the Open edX Nutmeg Release were nearly not uploaded because the
  edx-transifex-bot did not automatically upload it. In addition, the script for
  uploading the translations did not work, and the script had to be tinkered for the new
  Transifex API in order to upload it in time for release. The files uploaded were still
  not in the preferred format because the new version of the Transifex API does not have
  all the same features as the previous API version.

  * Running the script to upload translations requires Transifex admin rights, and there
    are few people with Admin rights to Transifex and knowledge of the Transifex API. We
    should use the solution provided by Transifex to solve this type of problem.

Rationale
*********

* This is an upgrade of a system we use regularly, but do not want to have to maintain
  regularly.
* Upgrading from a bot (machine user) to an app/workflow is recommended by GitHub and
  makes the translation process more open source. 
* The GitHub Transifex Integration App is developed and supported by Transifex
* The Github Transifex App is very simple to configure and with many options. We can set
  Transifex Projects to automatically upload/download translation files from a repository
  once the translations are reviewed and accepted.
* Transifex only allows a one-to-one relationship between repositories and Transifex
  Projects. Organizing all of the translation files into one repository and one Transifex
  Project has a lower cost: we pay by the project so we end up paying less, and by
  putting all translation files in the same place, it is easier to track translation
  progress, and debug translation issues.
* A repo that only contains text/binary files, and uses branches to separate translations
  related to Open edX releases can make all interactions with translations very quick and
  simple due to the ability to clone the branch of a specific release, with translations
  organized by repository name.
* By using an app that is maintained by Transifex the organization, we reduce the
  maintenance burden and are more future proof of changes they might make since they
  maintain both the API and the github App.

Proposed Implementation
***********************

* Move translation files to a new repo
* Add GitHub Transifex App to openedx organization
* Connect i18n repo to Transifex via app
* Set up for each release branch
* Create/Copy Transifex Memory

Pros & Cons
===========

Con: Impact on Translators
--------------------------

As we approach the end of the translation upgrade process, we will need to tactically
move from multiple transifex projects to a single project.  This will require
coordination with our translators to ensure that moving forward they are providing
translations in the right place.

Con: Impact on Site Operators
-----------------------------

Currently the translation files for any given service or library is stored at the same
place as the code, this has generally simplified the deployment story in the past.  With
this change, the translations files will move to their own repository.  As we deprecate
the old translations files, the relevant deployment tooling will need to be updated to
pull down the translations from the new repo as a part of the deployment process.  This
will impact both the old Ansible based tooling as well as any new docker based tooling.

Con: Impact on Developers
-------------------------

While it won’t directly impact the day-to-day workflow of developers, due to the same
reasons that we impact site operators(new translations location), we will have to update
development tools as well.

Locations
=========

Dumps of the translation/localization files from Transifex for the Open edX Release
project already exist in a repository with the name of openedx/openedx-i18n. A new
repository named openedx/openedx-translations, will be similarly structured, but contain
the translation files for all repos within openedx. The GitHub Transifex app will be
installed in the openedx organization. Similar to how the Build-Test-Release Working
Group creates a new branch for each new named release of edx-platform, translation
releases can also be kept in branches corresponding to edx-platform releases.

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

Making a Transifex Project for each Repository
==============================================

As translation support is provided for more repos, the total cost will also increase. A
Transifex Project houses the content to be translated and needs to be created before any
content can be added for translation. Transifex Projects can only support 1 GitHub
repository each and need to be maintained separately. Maintaining a Transifex Project
involves adjusting configurations, adding new languages, assigning translators to
projects, or any other miscellaneous irregular tasks that would be time-consuming at a
larger scale. If we adda a Transifex Project, each Transifex Project will need to be
maintained separately, making debugging issues or tracking the progress of each Transifex
Project time-consuming.
