Location of Decentralized Devstack Docs
=========================================

Status
------

Superceded by :doc:`0005-centralized-devstack.rst`

Context
-------

- While developing for Decentralized Devstack(DD), it became necessary to have a centralized location for documenting platform wide decisions and transitory how tos.

- OEP-5: Pre-built Developer Environments

  + this proposes a design for developer environments for Open edX. The document has now become outdated and many decisions relating to DD will require changes in OEP-5.

- We are still in prototype phase, so many of our current decisions are possibly very transient.


Decision
--------

Place DD documentation(general decisions, general how-tos) in edx/open-edx-proposals/oeps/oep-0005. Eventually, these decisions will be used to update OEP-5.

.. note::

    Any documentation specific to a particular repo should still go in that repo regardless of its relation to DD.


Rejected Alternatives
---------------------

edx/devstack repo: rejected because DD is meant to eventually displace current devstack. This was rejected to avoid creating dependencies between the two.

edx/edx-developer-docs repository: This as central repositories for platform wide documentation. This was heavily considered and still might eventually be the right location for the DD's how-tos.

In edx-cookiecutters/cookiecutter-django-ida/docs/decentralized_devstack: This was rejected because this cookiecutter is just one of many places that will be modified. Eventually, links should be created from the cookiecutter that point of location of these decisions.
