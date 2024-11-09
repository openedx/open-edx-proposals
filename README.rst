Open edX Proposals Index
########################

|license-badge| |doc-badge| |status-badge| |contributors-badge|

OEP (pronounced “oh-epp”) stands for Open edX (Enhancement) Proposal. An OEP is
a document that details a specific technology or community decision being made by
the Open edX community, in the form of a best practice, architecture design, or
process adjustment. An OEP should provide the use cases and rationales that surround
that choice. OEPs are not the only way for a change to be made to Open edX, however,
the goal is to create a collection of OEP documents as a repository or knowledge
archive of large and broadly relevant choices made for the platform.

View the published list of `Open edX Proposals (OEPs)`_ on ReadTheDocs.

.. _Open edX Proposals (OEPs): https://open-edx-proposals.readthedocs.io

Getting Started
***************

This repository holds a bunch of text files, each of which represent an OEP or
supplementary documentation. For browsing these OEPs, we recommend you read them
in `their published form <https://open-edx-proposals.readthedocs.io>`_ on ReadTheDocs.

Contributing
************

Contributions are very welcome.
Please read `How To Contribute <https://openedx.org/r/how-to-contribute>`_ for details.

OEPs are foundational *community* documents. The whole community is invited to collaborate
on these documents, from writing to fixing to updating to reviewing. Persons interested
in following OEP progress should join the `#open-edx-proposals Slack channel`_ and/or
follow the `Announcements category in the forums`_.

Any member of the community is welcome to propose changes or addendums to existing
OEPs, or to propose a brand new one! All that is needed is to fork this repo and
get writing. We use `ReStructured Text (RST)`_ to write all our OEPs, and we discuss
all proposed changes and additions on `pull requests`_.

.. _#open-edx-proposals Slack channel: https://openedx.slack.com/archives/C1L370YTZ
.. _Announcements category in the forums: https://discuss.openedx.org/c/announcements/17
.. _ReStructured Text (RST): https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html
.. _pull requests: https://docs.openedx.org/en/latest/developers/quickstarts/first_openedx_pr.html

Testing Locally
***************

Before you make a pull request with your proposed changes, please try to visually test your changes first.

To test locally in a Python virtual env, you will first need to install `GraphViz <http://graphviz.org/>`_
On a Mac, this can be done via ``brew install graphviz``; on Ubuntu, use ``sudo apt install graphviz``; on Red Hat variants use ``sudo dnf install graphviz``.
Next, run the following commands

.. code-block:: bash

  pip install sphinx  # it may fail for non-obvious reasons without this
  make requirements
  make html

Common Warnings
===============

Document isn't Included in any toctree
--------------------------------------

If you have some documents that you only reference via ``:doc:`` or ``:ref:`` tags you may get this error.
If there is no table of contents that the files obviously belong in, an easy way to fix this error is to put the
documents in a hidden toctree near where they are referenced:

.. code-block:: rst

    .. toctree::
        :hidden:

        path/to/referenced-file.rst

Getting Help
************

If you're having difficulties writing or testing, reach out to us on the
`discussion forums`_. Be sure to categorize your question appropriately -
if you're looking for technical help, the Development category is what you
want. If you're stuck on an idea and could use some community help in
bringing it to life, try the Collaborative Proposals category!

.. _discussion forums: https://discuss.openedx.org/

Code of Conduct
***************

All contributors to and reviewers of documentation in this repo must adhere
to the `Open edX Code of Conduct`_. If you witness a breach of the Code of
Conduct, please reach out to the maintainer of this repo or to the contacts
listed in the Code of Conduct text.

.. _Open edX Code of Conduct: https://openedx.org/code-of-conduct/

People
******

See the `open-edx-proposals Backstage page`_ for more information about who the
maintainers of this repo are and how to get in touch with them.

.. _open-edx-proposals Backstage page: https://backstage.openedx.org/catalog/default/component/open-edx-proposals

Reporting Security Issues
*************************

Please do not report security issues in public. Please email security@openedx.org.


.. |license-badge| image:: https://img.shields.io/badge/License-CC_BY--SA_4.0-lightgrey.svg
    :target: https://github.com/openedx/open-edx-proposals/blob/main/LICENSE.txt
    :alt: License

.. |doc-badge| image:: https://readthedocs.org/projects/open-edx-proposals/badge/?version=latest
    :target: https://open-edx-proposals.readthedocs.io/en/latest/
    :alt: Documentation

.. |status-badge| image:: https://img.shields.io/badge/Status-Maintained-brightgreen

.. |contributors-badge| image:: https://img.shields.io/github/contributors/openedx/open-edx-proposals.svg
