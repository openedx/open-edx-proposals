Location of Decentralized Devstack Docs
=========================================

Status
------

Accepted

Context
-------

- While developing for Decentralized Devstack(DD), it became necessary to have a centralized location for documenting platform wide decisions and transitory how tos.

- OEP-5: Pre-built Developer Environments

  + this proposes a design for developer environments for Open edX. The document has now become outdated and many decisions relating to DD will require changes in OEP-5.

- We are still in prototype phase, so many of our current decisions are possibly very transient.


Decision
--------

Place DD documentation/decisions in edx/open-edx-proposals/oeps/oep-0005. Eventually, these decisions will be used to update OEP-5.


Rejected Alternatives
---------------------

edx/devstack repo: rejected cause DD is meant to eventually displace current devstack. So creating dependencies between the two does not make sense.

edx/edx-developer-docs repository: These acts as central repositories for platform wide documentation, so this was heavily considered and still might eventually be the right location for the DD's how-tos .

In edx-cookiecutters/cookiecutter-django-ida/docs/decentralized_devstack: While the cookiecutter-django-ida will eventually be modified based on DD decisions, this cookiecutter is just one of many places that will be modified. Eventually, links shouldbe created from the cookiecutters that point of location of these decisions.
