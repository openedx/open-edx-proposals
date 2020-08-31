Location of Decentralized Devstack Docs
=========================================

Status
------

Accepted

Context
-------

- While developing for Decentralized Devstack(DD), it became necessary to have a centralized location for documenting platform wide decisions and transitory how tos.

- OEP-5: Pre-built Developer Environments



Decision
--------

Place DD documentation/decisions in edx-cookiecutters/docs/decentralized_devstack.


Rejected Alternatives
---------------------

edx/devstack repo: rejected cause DD is meant to eventually displace current devstack. So creating dependencies between the 2 does not make sense.

edx/edx-developer-docs or edx/open-edx-proposals repo: These both act as central repo for platform wide documentation, so this was heavily considered and still might eventually be the right location for these docs. But if we are following the recommendation of putting docs closer to the thing it affects, edx-cookiecutters wins the case.

In edx-cookiecutters/cookiecutter-django-ida/docs/decentralized_devstack: Rejected cause we might eventually have cookiecutters for MFE's. So DD decisions can be thought to affect multiple cookiecutters(even if they don't yet exist)
