0003 README specification
#########################

Status
******

** Accepted **

Context
*******

`OEP-55`_ states that one of the jobs of a maintainer is to maintain an
up-to-date and useful readme. While it laid out some possibilites about what
should go into a README, it did not go into much detail.

Decision
********

We will define a specification that READMEs in the Open edX project must
follow.  By having a single definition, we can make it easier for people to
navigate between projects and find the most important information quickly.

Specification
*************

The readme of a well-maintained repo should contain at least the following
sections.  Ideally in this exact order with additional sections added where it
makes sense based on the maintainers judgement.

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

* **Getting Started** - A section that details various aspects of getting
  started with this component.  Some common use caseses include, getting started
  with development or how to deploy this component.

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

   * **new-features accepted** - please discuss your new ideas with the
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

.. code-block:: rst

   Repo Name
   ##########

   | |License: AGPL v3| |Status| |Python CI| |Publish package to PyPi|

   .. |License: AGPL v3| image:: https://img.shields.io/badge/License-AGPL_v3-blue.svg
     :target: https://www.gnu.org/licenses/agpl-3.0

   .. |Python CI| image:: https://github.com/openedx/DoneXBlock/actions/workflows/ci.yml/badge.svg
     :target: https://github.com/openedx/DoneXBlock/actions/workflows/ci.yml

   .. |Publish package to PyPi| image:: https://github.com/openedx/DoneXBlock/actions/workflows/pypi-release.yml/badge.svg
     :target: https://github.com/openedx/DoneXBlock/actions/workflows/pypi-release.yml

   .. TODO Link to how we make these and have examples for the other standard states.
   .. |Status| image:: https://img.shields.io/badge/status-maintained-31c653

   Purpose
   *******

   A few sentences that tell you what this code does and when you might want to use it.

   Getting Started
   ***************

   Developing
   ==========

   How can a new developer get started with developing on this component?  Is
   there a short set of commands they could run? Is it complicated and this should
   be a link out to a published larger How-To?

   .. code:: bash

        git clone git@github.com:openedx/the_repo.git
        virtualenv venv/DoneXBlock/
        source venv/DoneXBlock/activate
        make upgrade
        make install
        make dev.run

   You can interact with the DoneXBlock in the Workbench by navigating to http://localhost:8000


   Deploying
   =========

   How can a new user, go about deploying this component? Is it just a few commands? Is there a larger how-to that should be linked here?

   For details on how to deploy this component, checkout the `deployment how-to`_

   .. _deployment how-to: https://docs.openedx.org/projects/this-project/how-tos/how-to-deploy-this-component.html


   Getting Help
   ************

   If you're having trouble, we have discussion forums at
   https://discuss.openedx.org where you can connect with others in the
   community.

   Our real-time conversations are on Slack. You can request a `Slack
   invitation`_, then join our `community Slack workspace`_.

   For anything non-trivial, the best path is to open an issue in this
   repository with as many details about the issue you are facing as you
   can provide.

   https://github.com/openedx/DoneXBlock/issues

   For more information about these options, see the `Getting Help`_ page.

   .. _Slack invitation: https://openedx.org/slack
   .. _community Slack workspace: https://openedx.slack.com/
   .. _Getting Help: https://openedx.org/getting-help

   Contributing
   ************

   Details about how to become a contributor to the Open edX project may
   be found in the wiki at `How to contribute`_

   .. _How to contribute: https://openedx.org/r/how-to-contribute

   This project is currently accepting all types of contributions, bug fixes,
   security fixes, maintenance work, or new features.  However, please make sure
   to have a discussion about your new feature idea with the maintainers prior
   to beginning development to maximize the chances of your change being
   accepted.  You can start a conversation by creating a new issue on this repo
   summarizing your idea.


   The Open edX Code of Conduct
   ****************************

   All community members are expected to follow the `Open edX Code of Conduct`_.

   .. _Open edX Code of Conduct: https://openedx.org/code-of-conduct/

   People
   ******

   The assigned maintainers for this component and other project details
   may be found in `Backstage`_. Backstage pulls this data from the
   ``catalog-info.yaml`` file in this repo.

   .. _Backstage: https://open-edx-backstage.herokuapp.com/catalog/default/component/this-component

   Reporting Security Issues
   *************************

   Please do not report security issues in public. Please email security@tcril.org.

Examples
========

* `DoneXBlock <https://github.com/openedx/DoneXBlock/blob/master/README.rst>`_

.. _OEP-55: https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0055-proc-project-maintainers.html
