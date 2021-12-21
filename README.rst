Open edX Proposals Index
========================

OEP (pronounced “oh-epp”) stands for Open edX (Enhancement) Proposal. An OEP is
a document that details a specific technology decision being made by the Open
edX community, in the form of a best practice, architecture design, or process
adjustment. An OEP should provide the use cases and rationales that surround that
choice. OEPs are not the only way for a change to be made to Open edX, however,
the goal is to create a collection of OEP documents as a repository or knowledge
archive of large and broadly relevant choices made for the platform.

View the published list of `Open edX Proposals (OEPs)`_ on ReadTheDocs.

.. _Open edX Proposals (OEPs): https://open-edx-proposals.readthedocs.io

Testing locally
---------------

To test locally in a Python virtual env, you will first need to install `GraphViz <http://graphviz.org/>`_
On a Mac, this can be done via ``brew install graphviz``; on Ubuntu, use ``sudo apt install graphviz``.
Next run the following commands::

  pip install sphinx  # it may fail for non-obvious reasons without this
  make requirements

  make html

Common Warnings
~~~~~~~~~~~~~~~

Document isn't Included in any toctree
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you have some documents that you only reference via ``:doc:`` or ``:ref:`` tags you may get this error.
If there is no table of contents that the files obviously belong in, an easy way to fix this error is to put the
documents in a hidden toctree near where they are referenced::

    .. toctree::
        :hidden:

        path/to/referenced-file.rst
