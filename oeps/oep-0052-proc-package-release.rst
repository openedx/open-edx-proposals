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
2. You’ll get a list of available Github workflow templates. Choose the ‘Publish Python Package’ workflow for Python package or ‘Publish Node.js Package’ workflow for JS package, by clicking on ‘Set up this workflow’ button.
3. Make any package specific changes in the workflow, if required.
4. Now click on the ‘Start Commit’ button at the right side of the screen. A dialog will appear. Write the commit message and description. Choose whether you want to merge it to master or create a new branch and start a PR. Then click on ‘Commit New File’.
5. Once this workflow is up on master, the above created Github Action will trigger and publish a package release
    * whenever you push a tag in the repository, in case of python package
    * whenever a new change is pushed on master, in case of JS package

Triggering a New Release
________________________

1. Make sure to bump the version number in the setup.py file.
2. Add the changelogs in 'CHANGELOG.md'/'CHANGELOG.rst' .
3. Commit your code and tag the commit with the new version number you just specified.
4. Push the tag on master branch.
