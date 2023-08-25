.. _adr_based_template:

.. Below is the display in the left sidebar on RTD. Please omit leading 0's

OEP-0066: Frontend Dependencies Update
######################################

.. This OEP template is based on Nygard's Architecture Decision Records.

.. list-table::
   :widths: 25 75

   * - OEP
     - Link to the doc in the following format::

        :doc:`OEP-XXXX <oep-XXXX-YYYY-ZZZZ>`

        * <XXXX is the next available OEP number>
        * <YYYY is the abbreviated Type: proc | bp | arch>
        * <ZZZZ is a brief (< 5 words) version of the title>

   * - Title
     - Frontend Dependencies Update
   * - Last Modified
     - 2023-07-17
   * - Authors
     - Muhammmad Abdullah Waheed <mawkhan@2u.com>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - Draft
   * - Type
     - Best Practice
   * - Created
     - 2023-07-17
   * - Review Period
     - <start - target end dates for review>

Abstract
********

Proposes best practices for maintaining dependencies on Javascript repositories of Open edX

Context
*******

Openedx micro-frontends(MFEs) use javascript. We maintain these repositories using  To upgrade its dependencies through package.json, we can either use `^` or `~` operators to automatically upgrade major and patch versions respectively. 
It might lead to some complexities since its hard to debug what upgrade actually caused the issue. 
To overcome this issue, we can use renovate bot. Renovate Bot is a popular GitHub app that automatically keeps your npm dependencies (including the package-lock.json) up-to-date. Once installed, Renovate Bot scans your repository for outdated packages and creates pull requests with updated versions. You can configure the bot's behavior through a renovate.json file in your repository.


Specification
*************

We can configure renovate bot in our github repositories to update npm packages automatically. 

Here are the advantages of using renovate bot:

Regular Updates
---------------
Renovate Bot creates pull requests with dependency updates. 
We can review these pull requests regularly to ensure that the updates are safe for our project or we can enable automerge. 

Configure Update Schedules
--------------------------
Customize the update schedules in the renovate.json file according to our project's needs. 
We can set up daily, weekly, or monthly update checks, depending on your development cycle and risk tolerance.

Automated Testing
-----------------
Set up automated tests using continuous integration (CI) tools to run on Renovate Bot's pull requests. 
This ensures that dependency updates don't introduce any breaking changes or issues.

Use Grouping
------------
Renovate Bot can group multiple dependency updates into a single pull request to avoid overwhelming your repository with a large number of pull requests.

Override Dependencies
---------------------
If you have specific requirements or need to stick to a particular version of a dependency, you can use the packageRules configuration in renovate.json to override how Renovate Bot updates specific packages.

Ignore Dependencies
-------------------
In some cases, you may want to exclude certain dependencies from being updated automatically. Use the ignoreDeps configuration in renovate.json to specify packages that Renovate Bot should not update.

Documentation
-------------
Renovate Bot PRs have very good documentation of changes with proper linking.

Best Practices
**************
We can configure Renovate Bot in our repositories by introducing `renovate.json` file. Here are some best practices on how to configure it.

We can enforce our repos to use basic configurations like schedule, automerging, rebasingStalePrs. 
In addition to that, we can also add package specific rules. For example

.. code-block::

  {
    "extends": [
      "config:base",
      "schedule:weekly",
      ":automergeLinters",
      ":automergeMinor",
      ":automergeTesters",
      ":enableVulnerabilityAlerts",
      ":rebaseStalePrs",
      ":semanticCommits",
      ":updateNotScheduled"
    ],
    "packageRules": [
      {
        "matchDepTypes": [
          "devDependencies"
        ],
        "matchUpdateTypes": [
          "lockFileMaintenance",
          "minor",
          "patch",
          "pin"
        ],
        "automerge": true
      },
      {
        "matchPackagePatterns": ["@edx", "@openedx"],
        "matchUpdateTypes": ["minor", "patch"],
        "automerge": true
      }
    ],
    "timezone": "America/New_York"
  }

We can set schedule of whole Renovate Bot or we can set independent schedules.
We can also set the automerge rules to be satisfied. 
Renovate allow us to set multiple package rules, for example, we can add a rule to auto update ``@edx`` namespaced minor and patch versions.

Consequences
************
If we have renovate configured in all of our frontend repos, we can standardise this across the board. 

References
**********

List any additional references here that would be useful to the future reader.
See `Documenting Architecture Decisions`_ for further input.

.. _Documenting Architecture Decisions: https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions

Change History
**************


YYYY-MM-DD
==========


* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
