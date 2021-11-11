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
|               | Muhammad Aarif <aarif@edx.org>                               |
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

This document proposes a standard package release process to be followed across the Open edX community.

Motivation
==========

We recently shifted the CI from Travis to Github Actions which led us to move the package release process to Github Actions as well. While making this change, we encountered different approaches used for this task in different packages, leading to confusions. As new releases for packages are published from time to time by the Open edX community, we need to ensure that a consistent approach is used by edX engineers to make these releases. Here we have documented a standard approach for this purpose.

Specification
=============

Github has a feature to create workflow templates to be used across the organization, which helps standardize the processes. We’ve written some workflow templates to automate the package release process in edx owned packages. Some of these templates use reusable Github workflows and custom Github actions to achieve the goal while keeping a single source of truth and makes workflow templates simple.

Python Packages
---------------

To set up the release process in python packages, you need to add the following workflow templates in package’s repository (if you need help for using workflow templates, see the `documentation`_):

1. Generate Changelogs
~~~~~~~~~~~~~~~~~~~~~~

The `Generate Changelogs`_ workflow takes the responsibility to create a PR with version bump and new changelogs. You must have ``CHANGELOG.rst`` in the target repository and you need to make the following changes as well, when adding this workflow to the repository.

* Add the comment ``<New logs>`` in CHANGELOG.rst where you want to add new changelogs.

.. code-block::

    Unreleased
    ----------

    *

     .. <New logs>

     [4.0.1] - 2021-11-01
    --------------------

    Changed
    ~~~~~~~

    * Resolve RemovedInDjango4.0 warnings.


* In setup.cfg add the configurations for semantic-release.
    - ``version_variable``  Path of the version variable
    - ``commit_author``     Username and email to be used for commits

.. code-block::

    [semantic_release]
    version_variable = auth_backends/__init__.py:__version__
    commit_author = github-actions <actions@github.com>

To create the required PR, it follows the following steps:

1. Use ``python-semantic-release`` package to check the commit messages since the last tag and determine whether a version bump is required.
2. If the tool suggests a version bump, update the value of the version variable in the path specified in ``setup.cfg``.
3. Call the python script ‘changelogs.py’ to generate changelogs in md format using ``python-semantic-release``, convert them to rst using ``pandoc`` and then add them to the ``CHANGELOG.rst`` where ``.. <New logs>`` is mentioned.
4. Create a PR with the above changes and use the username and email mentioned in ``setup.cfg``.

2. Tag and Release
~~~~~~~~~~~~~~~~~~

`Tag and release`_ workflow gets triggered when the PR created by above workflow is merged. This workflow tags the commit and creates a Github release.

3. Publish Python Package
~~~~~~~~~~~~~~~~~~~~~~~~~

`Publish Python Package`_ workflow gets triggered whenever a tag is pushed. It publishes the package with changes made in the latest tag.

JS Packages
-----------

To set up the release process in JS packages, you need to add the `Publish Node.js Package`_ workflow. Whenever a commit is pushed on master, the ‘Publish Node.js Package’ workflow will decide whether the version bump is required or not. If the version gets bumped, a new release will be published on npm. This workflow uses JS ``semantic release`` package to achieve its goals.

Rationale
=========

Currently, the package release process comprises a number of manual steps. Once the changes are pushed, we need to update changelogs, bump version number then create tag or github release which finally triggers the final package release. Moreover, we prefer to publish releases on tags but also want to keep github releases aligned with PyPI releases so that release history could be found in the github repository as well.
By using the release process proposed in the OEP we’ll be able to achieve the following:

* Just push the changes and the release pipeline will automatically take care of the rest of the process.
* Trigger release on tag creation
* Keep Github releases aligned with the PyPI releases
* Encourage usage of conventional commits


Alternatives Considered
=======================

We also considered triggering the ‘Publish python package’ workflow on Github release but creating a Github Release seemed to be an extra step for those who aren’t using ‘Tag and Release’ workflow in their package as they had to create a tag first and then create a Github release to trigger PyPI release. Now, we are initiating PyPI releases on tags so that creating Github releases doesn’t become a necessary step but remains a best practice only.


.. _documentation: https://docs.github.com/en/actions/learn-github-actions/using-workflow-templates
.. _Generate Changelogs: https://github.com/edx/.github/blob/5ac1c8f213d2d29c944de3751132ce937c1f3ddc/workflow-templates/changelogs.yml
.. _Publish Node.js Package: https://github.com/edx/.github/blob/master/workflow-templates/npm-publish.yml
.. _Publish Python Package: https://github.com/edx/.github/blob/master/workflow-templates/pypi-publish.yml
.. _Tag and release: https://github.com/edx/.github/blob/5ac1c8f213d2d29c944de3751132ce937c1f3ddc/workflow-templates/tag-version.yml