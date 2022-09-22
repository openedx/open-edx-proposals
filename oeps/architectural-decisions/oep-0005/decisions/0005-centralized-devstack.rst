Deciding Not To Use Decentralized Devstack
##########################################


Status
******

Accepted

Context
*******

In `decision 0002`_ we argued for replacing `Devstack`_ with Decentralized Devstack, a framework for devstack where each IDA would have and own its own devstack. To test out the new framework, a prototype was created for enterprise-catalog. Our initial spike showed that the reduction in capabality that resulted from moving to this approach was too large for the gains it provided. We discoveredthat developers expected to be able to make modifications across multiple IDAs during development, which did not lend itself to the distributed devstack model.

Decision
********

We will continue to maintain a centralized devstack repo.


Consequences
************

All files/changes related to implementation of Decentralized Devstack will be removed, and the decisions hilighted in decisions 0001 and 0002 are now reversed.

.. _Devstack: https://github.com/openedx/devstack
.. _decision 0002: https://open-edx-proposals.readthedocs.io/en/latest/oep-0005/decisions/0002-why-decentralized-devstack.html
