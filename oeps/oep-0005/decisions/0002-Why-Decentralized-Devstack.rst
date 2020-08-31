Why Decentralized Devstack
==========================

Status
------

Accepted

Context
-------

Devstack is the centralized set of docker images, make files, and bootstrap scripts used to set up an environment that engineers can run locally to develop edX services in a consistent manner from engineer to engineer.

The following pain points have been identified in our current development environment:

- Operational concerns like schema migrations, data seeding, and configuration are opaque and not consistent between applications.
- The centralized and unversioned nature of the devstack means that changes made by one team can cause unintended  knock-on effects for other teams
- The operations required to setup devstack take a long time even when someone knows exactly what to do.  Longer if there is trial and error involved for engineers who are new to interacting with the stack.
- The system resources required to run the stack are more than some edX issued high-end laptops can handle. 
Dockerfiles and corresponding images are opaque to developers and challenging to change/troubleshoot.

Some of the challenges listed above are due to the design of the devstack development environment itself.  Others are inherent in the design of the applications.  Both will need to evolve to solve for all of the most common pain points.  


Decisions
---------

Decentralize current devstack and allow each codebase to define its own development environment based on its dependencies.


Technical Vision for a development environment
----------------------------------------------

- Starts up quickly and uses few enough resources that developers can run it with common development tools (e.g. an IDE) on the lowest-common-denominator development laptop.
- Engineers can efficiently change many different services without having to spend a lot of time learning about each one

  + this will be done by standardizing interface for start up and common interactions(like tailing the logs).

- Schema consistent with production
- No manual data entry required for standard, reasonably production-like, configurations
- Path for reproducing data for custom configurations
- Pattern for developing that includes path for private/custom dependencies


Rejected Alternative
--------------------

Improve Current Devstack: TODO: give quick summary of why this was rejected


Sources
-------

Apologizes: some of this might just be available to edX employees.

`Devstack 2.0 One-Pager`_: Document explaining scope of Devstack 2.0 work

`Decentralized DevStack Team Report`_: Document explaining work done by Devstack spike team

.. _Devstack 2.0 One-Pager: https://docs.google.com/document/d/1DFOeFciqfFBXltZFyFEuQcArRpAh5XcP_yG2Bs7-TUI/edit?ts=5f10b819

.. _Decentralized DevStack Team Report: https://docs.google.com/document/d/18QfA3zv7RCb-wjHws4egOE1wFLNa6GiCqBGKBTejss0/edit#
