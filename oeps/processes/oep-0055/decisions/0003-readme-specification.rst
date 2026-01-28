.. _README specification:

0003 README specification
#########################

Status
******

**Accepted**

Context
*******

`OEP-55`_ states that one of the jobs of a maintainer is to maintain an
up-to-date and useful readme. While it laid out some possibilities about what
should go into a README, it did not go into much detail.

`OEP-19`_ also suggests that README files should exist but does not provide a
lot of guidance on specifics.

Decision
********

We will define a specification that READMEs in the Open edX project must
follow.  By having a single definition, we can make it easier for people to
navigate between projects and find the most important information quickly.

Specification
*************

The readme of a well-maintained repo should contain at least the following
sections (an example template follows this section). Ideally in this exact
order with additional sections added where it makes sense based on the
maintainers judgement.

* **Title** - The name of the component.

* **Badges** - A set of `badges <https://github.com/badges/shields>`_ that
  quickly let visitors review the health of the project.

	 * The badges section should contain at minimum the following badges:

		  * **License** - Indicating the license of the repo.

		  * **Maintenance Status** - One of ``maintained``, ``deprecated``, ``unsupported``, ``experimental``.

		  * **CI Status** - The status of CI testing on the main branch.

		  * **Package Published Status** - If the component is a library, then a badge indicating whether publishes to the relevant package index are passing. For example, has the latest version been successfully published to PyPI or NPM?


* **Purpose** - A paragraph or two summarizing the purpose of this component.
  This should be oriented towards people who are new to the Open edX project.

* **Getting Started with Development** - A section that helps developers get oriented. Unless the repo has unusual workflows, this might be as simple as linking to the `Python <https://docs.openedx.org/en/latest/developers/how-tos/get-ready-for-python-dev.html>`_ or `frontend <https://docs.openedx.org/en/latest/developers/how-tos/get-ready-for-frontend-dev.html>`_ developer how-to.

* **Getting Help** - A section to indicate where and how the user can get help
  with the component, including how they should go about reporting issues with
  the component. It should link to relevant docs as well as places where the
  users can get help from other humans.

* **Contributing** - A section to indicate how new users can contribute to
  this component.  This section should at the very least contain a link to the
  general `How to Contribute <https://openedx.org/r/how-to-contribute>`_
  documentation for the Open edX Project.

  The level of contributions that the maintainer is willing to accept should
  also be clearly described here.  Some possible values might be:


   * **fixes only** - no new features are being accepted at this point, but fixes
     and maintenance updates welcome.

   * **new features accepted** - please discuss your new ideas with the
     maintainers before you write code to increase the changes that features are
     accepted.

   * **security only** - no changes accepted except for security fixes.

* **Open edX Code of Conduct** - A section that links to the `Open edX Code of
  Conduct <https://openedx.org/code-of-conduct/>`_

* **People** - A section that tells people how they can find the current
  maintainers of this component.  Usually a link to the Backstage page for the
  component.

* **Reporting Security Issues** - A link to the e-mail address where security
  issues can be reported.

Template
========
If you have an existing repository that needs to update its README.rst file to
match the standards defined here.  You can begin by copying the `templated
README.rst`_ from the cookiecutter repository used to create new Open edX repos.

.. _templated README.rst: https://raw.githubusercontent.com/openedx/edx-cookiecutters/master/python-template/%7B%7Bcookiecutter.placeholder_repo_name%7D%7D/README.rst

Examples
========

* `DoneXBlock <https://github.com/openedx/DoneXBlock/blob/master/README.rst>`_

.. _OEP-55: https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0055-proc-project-maintainers.html
.. _OEP-19: https://docs.openedx.org/projects/openedx-proposals/en/latest/best-practices/oep-0019-bp-developer-documentation.html

