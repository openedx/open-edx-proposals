Location of Decentralized Devstack Docs
=========================================

Status
------

Accepted

Context
-------

- While developing for Decentralized Devstack(DD), it became necessary to have a centralized location for documenting platform wide decisions and transitory how tos.

- edx/edx-cookicutters repository exists as a starting point for a new edx project. It also acts as a place where we document our current platform wide standards. We will eventually want to modify the cookiecutters in this repository to use Decentralized Devstack(DD) pattern(if applicable)

- We generally try to put documentation as close to the source as possible. DD decisions will affect IDAs throughout the platform, but edx/edx-cookiecutters will act as ideal basic standard for the IDAs.



Decision
--------

Place DD documentation/decisions in edx-cookiecutters/docs/decentralized_devstack.


Rejected Alternatives
---------------------

edx/devstack repo: rejected cause DD is meant to eventually displace current devstack. So creating dependencies between the 2 does not make sense.

edx/edx-developer-docs or edx/open-edx-proposals repo: These both act as central repo for platform wide documentation, so this was heavily considered and still might eventually be the right location for these docs. But if we are following the recommendation of putting docs closer to the thing it affects, edx-cookiecutters wins the case.

In edx-cookiecutters/cookiecutter-django-ida/docs/decentralized_devstack: Rejected cause we might eventually have cookiecutters for MFE's. So DD decisions can be thought to affect multiple cookiecutters(even if they don't yet exist)
