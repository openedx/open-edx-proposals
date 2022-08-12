======================================
OEP-53: Python Package Release Process
======================================

+---------------+--------------------------------------------------------------+
| OEP           | `OEP-53 <oep-0053>`                                          |
+---------------+--------------------------------------------------------------+
| Title         | Python Packages' Release Process                             |
+---------------+--------------------------------------------------------------+
| Last-Modified | 2022-02-09                                                   |
+---------------+--------------------------------------------------------------+
| Authors       | Jawayria Hashmi <jhashmi@edx.org>,                           |
|               | Aarif <aarif@edx.org>                                        |
+---------------+--------------------------------------------------------------+
| Arbiter       |                                                              |
+---------------+--------------------------------------------------------------+
| Status        | Draft                                                        |
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

This document proposes a standard Python package release process to be followed across the Open edX community.

Motivation
==========

We recently shifted the CI from Travis to GitHub Actions which led us to move the package release process to GitHub Actions as well. During this process,
we observed some problems in the current package release process. The package release process is comprised of several manual steps.
Once the changes are pushed, we update the changelogs, bump the version number then create a tag or GitHub release which finally triggers the final package release.
As all these steps need to be done manually, sometimes things get missed. For instance, you made changes, bumped the version, and created a release but forgot
to add changelogs. Moreover, we prefer to publish releases on tags but also want to keep GitHub releases aligned with PyPI releases so that release history
could be found in the GitHub repository as well.

To solve these above-mentioned issues, we have automated the package release process. Initially, we looked for packages that could do the job but the
available packages don't provide much control so we created a pipeline using a set of GitHub workflows to achieve the goal. By using the release process
in this OEP we’ll be able to achieve the following:

* Just push the changes and the release pipeline will automatically take care of the rest of the process.
* Trigger release on tag creation
* Keep GitHub releases aligned with the PyPI releases
* Encourage usage of conventional commits

Specification
=============

GitHub has a feature to create workflow templates to be used across the organization, which helps standardize the processes. We’ve written some workflow
templates to automate the package release process in edX owned python packages. Some of these templates use reusable GitHub workflows and custom GitHub actions
to achieve the goal while keeping a single source of truth and making workflow templates simple.

How to set up?
--------------

To set up the release process in python packages, you need to add the following workflow templates in package’s repository
(if you need help for using workflow templates, see the `documentation`_):

1. `Generate Changelogs`_ - This workflow takes the responsibility to create a PR with version bump and new changelogs.
You must have ``CHANGELOG.rst`` in the target repository and you need to make the following changes as well, when adding this workflow to the repository.

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


* In setup.cfg add the configurations for semantic-release as shown below for auth-backends repository.
    - ``version_variable``  Path of the version variable
    - ``commit_author``     Username and email to be used for commits

.. code-block::

    [semantic_release]
    version_variable = auth_backends/__init__.py:__version__
    commit_author = github-actions <actions@github.com>

2. `Tag and release`_  - This workflow tags the commit and creates a GitHub release.

3. `Publish Python Package`_  - This workflow publishes the package on PyPI.

Technical Details
-----------------

* Whenever a commit gets pushed on master, the ‘Generate Changelogs’ workflow gets triggered and uses the 'python-semantic-release’ package to determine whether a version bump is required, based on the commit messages since the last tag. The new version is decided according to commit type as follows:

    - PATCH:	fix, perf
    - MINOR:	feat
    - MAJOR:	breaking change

* If the tool suggests a version bump, the ‘generate-changelogs’ reusable workflow is called from ‘edx/.github’ and it updates the value of the version variable in the path specified in setup.cfg.
* After the version bump, a custom GitHub action called ‘changelogs-generator’ is called. This action uses the python script ‘changelogs.py’ to generate changelogs in markdown format using ‘python-semantic-release’, convert them to reStructuredText format using ‘pandoc’ and then add them to the ‘CHANGELOG.rst’ where ‘.. <New logs>’ is mentioned.
* Note that changelogs are generated based on conventional commits. The commit type goes to the heading and subject line goes under it. The generated changelogs won't include the description of the commit message or any information from PR description. For instance, the current version is 3.2.4 and a commit was pushed with message "feat: Added a new xyz feature" on 2022-08-16. The changelogs added to ‘CHANGELOG.rst’ will have the following format:

.. code-block::

    [3.3.0] - 2022-08-16
    --------------------

    Feature
    ~~~~~~~

    -  Added a new xyz feature
       (`36afd12 <https://github.com/edx/{repo-name}/commit/{commit-id}>`__)

* Finally, a PR is created with the version bump and new changelogs. This PR uses the username and email mentioned in setup.cfg.
* Tag and release workflow gets triggered when the PR created by the above workflow is merged. This workflow tags the commit and creates a GitHub release.
* When a new tag is pushed, ‘Python Package Release’ workflow gets triggered and it publishes the package on PyPI with changes made in the latest tag.

Alternatives Considered
=======================

Some of the alternatives considered during the development of this package release pipeline are as follows:

* We considered triggering the 'Publish python package' workflow on GitHub release but creating a GitHub Release seemed to be an extra step for those who aren’t using 'Tag and Release' workflow in their package as they had to create a tag first and then create a GitHub release to trigger the PyPI release. Now, we are initiating PyPI releases on tags so that creating GitHub releases doesn’t become a necessary step but remains a best practice only.
* We tried to use `python-semantic-release`_ action directly instead of customization but we had to use a part of it and customize the rest of the things due to following reasons:
    - We wanted to create a PR with version bump and new changelogs so that we could update it if there’s anything not as expected and then continue the release process when this PR gets merged but the ‘python-semantic-release’ action takes care of the whole process itself.
    - The action generates changelogs in markdown format but in most of our repos we’ve changelogs in reStructured Text format
* Initially we were using `gitversion`_ for version bump and `git-chglog`_ for generating changelogs but then we shifted to 'python-semantic-release' as it could be used for both version bump and changelogs generation. Moreover, we had to install ‘git-chglog’ using homebrew which required setting up homebrew as well whereas ‘python-semantic-release’ is available on pip.


.. _documentation: https://docs.github.com/en/actions/learn-github-actions/using-workflow-templates
.. _Generate Changelogs: https://github.com/edx/.github/blob/5ac1c8f213d2d29c944de3751132ce937c1f3ddc/workflow-templates/changelogs.yml
.. _Publish Node.js Package: https://github.com/edx/.github/blob/master/workflow-templates/npm-publish.yml
.. _Publish Python Package: https://github.com/edx/.github/blob/master/workflow-templates/pypi-publish.yml
.. _Tag and release: https://github.com/edx/.github/blob/5ac1c8f213d2d29c944de3751132ce937c1f3ddc/workflow-templates/tag-version.yml
.. _git-chglog: https://github.com/git-chglog/git-chglog
.. _gitversion: https://gitversion.net/docs/
.. _python-semantic-release: https://python-semantic-release.readthedocs.io/en/latest/
