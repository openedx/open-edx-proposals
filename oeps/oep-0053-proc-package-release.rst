===============================
OEP-53: Package Release Process
===============================

+---------------+--------------------------------------------------------------+
| OEP           | `OEP-53 <oep-0053>`                                          |
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

Github has a feature to create workflow templates to be used across the organization, which helps standardize the processes. We’ve written some workflow templates to automate the package release process in edx owned packages.

**For Python Packages:**

To set up the release process in python packages, you need to add the following workflow templates in package’s repository (if you need help for using workflow templates, see here https://docs.github.com/en/actions/learn-github-actions/using-workflow-templates) :

1. Generate Changelogs - To figure out new version and generate changelogs
2. Tag and Release - Tag the commit and create Github release
3. Publish Python Package - To publish the package's latest version to PyPI.

**For JS Packages:**

To set up the release process in JS packages, you need to add the ‘Publish Node.js Package’ workflow.

How it works
============

**Python Packages**

1. Whenever a new ‘conventional’ commit is pushed on master, the ‘Generate Changelogs’ workflow checks whether a version bump is required or not. If the version needs to be bumped, it creates a PR with version bump and new changelogs in the CHANGELOGS.rst file.
2. Once the PR created by the ‘Generate Changelogs’ workflow gets merged, the ‘Tag and Release’ workflow creates a new tag and a Github release.
3. The ‘Publish python package’ workflow triggers on creating tag and publishes the release on PyPI (in case of python package).

**JS Packages**

Whenever a commit is pushed on master, the ‘Publish Node.js Package’ workflow will decide whether the version bump is required or not. If the version gets bumped, a new release will be published on npm.

Alternatives Considered
=======================

We also considered triggering the ‘Publish python package’ workflow on Github release but creating a Github Release seemed to be an extra step for those who aren’t using ‘Tag and Release’ workflow in their package as they had to create a tag first and then create a Github release to trigger PyPI release. Now, we are initiating PyPI releases on tags so that creating Github releases doesn’t become a necessary step but remains a best practice only.
