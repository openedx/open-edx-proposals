0002 Maintainership Transfer Process
####################################

Status
******

**Accepted**

Context
*******

As a part of the lifecycle of maintainership in the Open edX platform, we need
a process by which the current maintainers can give up maintainership of a
component.  We want to define a process where, when the current maintainer can
no longer support a component that the maintainership can pass to someone in
the community who can or a process is defined if no one wants to maintain the
component.

Decision
********

We will define a simple process for seeking a new maintainer as well as what to
do if a new maintainer cannot be found.

Process Definition
******************

.. graphviz::
   :alt: A flowchart graphically representing the maintainership transfer process.

   digraph maintainership_transfer_process {
      layout=dot
      node [shape=rect style=rounded]
      [fontname=Arial]

      "Announce" -> "Wait"
      "Wait" -> {"Transfer" "Deprecate"}
   }

.. _oep-55-adr-2-announce:

Announce
========

Communicate on the `forums`_ that ``component X`` is seeking a new maintainer.

.. code::

   Hello, I'm the current maintainer of `component X`.  I am no longer able to
   dedicate the amount of time needed to properly maintain this component to the
   level described in (OEP-55)[https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0055-proc-project-maintainers.html]
   and I am seeking a new maintainer.

   Code: /link/to/codebase
   Docs: /link/to/docs

   On <date 2 weeks in the future> if now new maintainer can be found I'll begin
   putting `component X` through the deprecation and removal process.

.. _forums: https://discuss.openedx.org/c/announcements/deprecation/20

Wait
====

It is recommend that similar to the Deprecation process, a wait period of at least 2 weeks should be given to give the community ample time to respond.

* Monitor responses

* Respond to questions

Transfer
========

If a new maintainer is found, initiate the transfer process.

.. graphviz::
   :alt: A chart defining the parts of the transfer process.

   digraph transfer_process {
      layout=dot
      node [shape=rect style=rounded]
      [fontname=Arial]

      "Announce" -> "Update GitHub Permissions"
      "Update GitHub Permissions" -> "Announce Transfer Completion"

   }

#. Once you know who the new maintainer is, announce the decision on the discourse thread created in the :ref:`oep-55-adr-2-announce` phase.

#. Add the new maintainer(s) to the relevant maintainers GitHub group.  ``<component_name>-maintainers``

#. (Optional but recommended) Meet with the new maintainers and review what is being transferred and any relevant knowledge that needs to be transferred.

#. Ask the new maintainers to remove you from the maintainers group.

#. The new maintainer will announce that the transfer is complete.

Deprecate
=========

If you were unable to find a new maintainer for a component, then it is your responsibility to put the component through `the deprecation and removal(DEPR) process`_

#. Update the thread created in the :ref:`oep-55-adr-2-announce` phase to communicate that no new maintainer was found, so the component will be deprecated and removed.  This thread will now serve the purpose of communicating the deprecation.

#. Follow the `DEPR`_ process.


Consequences
************

* If a new owner of a component cannot be found, it must be put through the full deprecation process by the current maintainer.

Rejected Alternatives
*********************

Integrate the Maintainership Transfer process into the DEPR Process
===================================================================

* The existing `DEPR`_ process and its documentation is oriented fully around the removal of code, updating it would require a significant re-write of the `DEPR`_ OEP.
* The overlap between what `DEPR`_ needs and what :ref:`OEP-55 Project Maintainers` needs is fairly small, mostly around announcing.
* It's easy to have a simple transfer process that feeds into the existing DEPR process.

References
**********

* Link to the deprecation process.

.. _the deprecation and removal(DEPR) process: https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0021-proc-deprecation.html
.. _DEPR: https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0021-proc-deprecation.html
