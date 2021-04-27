===============================
OEP-52: Package Release Process
===============================

+---------------+--------------------------------------------------------------+
| OEP           | `OEP-52 <oep-0052>`                                          |
+---------------+--------------------------------------------------------------+
| Title         | Package Release Process                                      |
+---------------+--------------------------------------------------------------+
| Last-Modified | 2021-04-07                                                   |
+---------------+--------------------------------------------------------------+
| Authors       | Jawayria Hashmi <jhashmi@edx.org>,                           |
|               | Muhammad Arif <aarif@edx.org>                                |
+---------------+--------------------------------------------------------------+
| Arbiter       |                                                              |
+---------------+--------------------------------------------------------------+
| Status        |                                                              |
+---------------+--------------------------------------------------------------+
| Type          | Process                                                      |
+---------------+--------------------------------------------------------------+
| Created       | 2021-04-07                                                   |
+---------------+--------------------------------------------------------------+
| Review Period |                                                              |
+---------------+--------------------------------------------------------------+
| References    |                                                              |
+---------------+--------------------------------------------------------------+

Abstract
========

This document proposes a standard package release process to be followed across the edX community.

Motivation
==========

We recently shifted the CI from Travis to Github Actions which led us to move the package release process to Github Actions as well. While making this change, we encountered different approaches used for this task in different packages, leading to confusions. As new releases for packages are published from time to time by the edX community, we need to ensure that a consistent approach is used by edX engineers to make these releases. Here we have documented a standard approach for this purpose.

Setting up the Github Workflow
==============================

1. Go to the Actions tab in your package’s repository. Click on the ‘New Workflow’ button.
2. You’ll get a list of available Github workflow templates. Choose the ‘PyPI Package release Workflow’ for Python package or ‘JS package release workflow’ for JS package, by clicking on ‘Set up this workflow’ button.
3. Make any package specific changes in the workflow, if required.
4. Now click on the ‘Start Commit’ button at the right side of the screen. A dialog will appear. Write the commit message and description. Choose whether you want to merge it to master or create a new branch and start a PR. Then click on ‘Commit New File’.
5. Once this workflow is up on master, whenever you create a new Github Release for the repository, the above created Github Action will trigger and create a package release on PyPI.

Triggering a New Release
________________________

1. Make sure to bump the version number in the setup.py file.
2. Commit your code and tag the commit with the new version number you just specified.
3. Create a github release on this tag and mention a summary and description for the release.
4. Once you publish the Github Release, the release on PyPI will also get triggered.

Github Release Summary and Description
======================================

* Summary is just a one line description of the major change made in the release.
* Github release description should contain the following:
   * New features
   * Bug fixes
   * Breaking changes
   * Improvements
* It is a good practice to use plain language and add any additional resources if needed to make it more comprehensive

Other Alternatives Considered
=============================

We considered triggering a PyPI release on Tag creation as we used to do in Travis. But we observed that engineers sometimes forget to create a Github Release when they tag a commit so we have some PyPI releases without any Github release in parallel. As the best practice is to create a Github Release whenever a PyPI release is triggered, we selected Github Release as the event triggering the action instead of Tagging.
