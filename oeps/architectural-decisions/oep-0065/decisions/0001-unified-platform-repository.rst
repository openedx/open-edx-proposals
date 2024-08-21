OEP-65 Create a unified platform repository
###########################################

Status
******

Accepted

Summary
*******

This ADR proposes combining a number of repositories into a single, unified frontend platform library as a migration strategy for implementing OEP-65.

Context
*******

OEP-65 proposes adopting webpack module federation for Open edX micro-frontends (MFEs) as a means to enable *runtime module loading* and *shared dependencies* in the Open edX frontend.  The OEP lays out a series of changes necessary to enable these capabilities, which it refers to as building a "reference implementation" of runtime module loading and shared dependencies.  This reference implementation is effectively a new underlying architecture for our frontend. This ADR refers to this new architecture as the "module MFE" architecture, as opposed to the historical "application MFE" architecture that has existed prior to OEP-65.

The bulk of the work to build OEP-65's reference implementation is related to the *library repositories* that MFEs depend on:

* frontend-build
* frontend-platform
* frontend-plugin-framework
* frontend-component-header
* frontend-component-footer

It will also involve the creation of a new *shell application* to initialize the frontend at runtime and load existing MFEs as modules.  Prior to this decision, we might have considered putting the shell application in its own repository as well:

* frontend-app-shell

There are several related concerns which influence our strategy for migrating these library repositories to the new module MFE architecture, and which influence where we decide to put the shell.

Significant API changes
=======================

The creation of the shell application and the move toward using plugins and module federation is expected to significantly change the API surface across our various libraries.

``frontend-platform``
---------------------

As the shell takes responsibility for much of what ``frontend-platform`` provides to MFEs in the application MFE architecture, many of the APIs it exposes will become internal to the shell.  We will also likely need to add new APIs for interactions between the shell and MFEs.

``frontend-build``
------------------

The development environment will also change, as we want to be able to run a single development server that loads the MFE under development into the shell.  This means creating alternatives to frontend-build's existing helpers to support the new architecture.

``frontend-component-header`` / ``frontend-component-footer``
-------------------------------------------------------------

The header and footer will be owned by the shell and need not be libraries any more.  A caveat to this is, of course, that we need to maintain good ways to customize them.

``frontend-plugin-framework``
-----------------------------

We expect ``frontend-plugin-framework`` to be folded into ``frontend-platform`` to unify the two, as they will be intimately tied to each other going forward.  We'll also be creating new APIs around loading plugins that support module federation.

``frontend-app-*`` Repositories
-------------------------------

While we don't expect to merge these into a unified library, they are the consumers of all the API changes above.  We expect it will be simpler for developers to absorb these changes - and the inevitable bug fixes - by updating a single library dependency, rather than trying to navigate an interconnected dependency tree with cascading version updates.

Reducing Dependency Maintenance
===============================

One of the motivators of OEP-65 was reducing dependency maintenance.  Not explicitly called out in the OEP is the desire to have fewer repositories to manage, which will in turn further reduce the number of dependencies in MFEs.  We see value in combining the library repositories listed above into a single library, which will in turn reduce the number of dependencies to manage in downstream repositories.

Modernization
=============

We also feel the need to continue to modernize our library repositories by adopting industry standard technologies like Typescript, or more performant webpack loaders, and there's some sentiment that this may be the right time to make these changes as we're already undergoing a paradigm shift.  ``frontend-platform``, for instance, simplifies significantly if we use TypeScript types instead of the bespoke "interface" and "service implementation" system in that repository.

Deprecation of the application MFE architecture
===============================================

As described in OEP-65, we're migrating from an "application" MFE architecture, where each MFE is a standalone, independently deployed app, to a "module" MFE architecture where the MFEs are modules loaded into a common "shell" application which owns the header, footer, initialization, and shared libraries.

One way or another, this is a paradigm shift that will involve breaking changes and migration work on behalf of community members.  We would like to provide a clear a path forward for operators, developers, and maintainers to adopt the module MFE architecture.  We believe that a clean break and a cohesive and clear platform for module MFEs is more approachable than a more granular deprecation of certain features and code in the existing library repositories.

Decision
********

We will migrate our existing foundational library repositories to the new module MFE architecture by creating a new, unified platform library to act as the primary dependency of module MFE frontends.

This library will include the parts of ``frontend-build``, ``frontend-platform``, ``frontend-plugin-framework``, ``frontend-component-header``, and ``frontend-component-footer``, as well as the new shell application that make sense for the module MFE architecture, along with new features and capabilities necessary to implement our vision.  We will also opportunistically fold in ``eslint-config``, which sees very little development but is its own source of dependency management overhead.

This new library will be released as one npm package with the following responsibilities.

Runtime library
===============

The library will export a subset of the APIs in ``frontend-platform``, along with ``frontend-plugin-framework``'s API and new APIs specific to the module MFE architecture and the shell.  This library will be a dependency of MFEs, as ``frontend-platform`` has been historically.

Development tool configurations
===============================

The library will provide base ESLint, Jest, TypeScript, and Webpack configurations.  ESLint, Jest, and TypeScript will be similar to what ``frontend-build`` provides.

The webpack configurations will support a variety of build targets for the new module MFE architecture, as well as application MFE configurations to enable us to migrate to the new unified platform library in a backwards compatible way.  The new build targets include:

* A production configuration suitable for deploying the MFE's modules to be consumed via module federation.
* A release configuration which will package those same modules to be released as an npm package for use as direct plugins.
* A development server configuration to serve the MFE's modules to be consumed locally via module federation, complete with hot module reloading.
* A development server configuration to serve the MFE's modules inside a shell as direct plugins.

These webpack configurations will be exposed via a function like ``frontend-build``'s '``createConfig`` so that they can be modified and extended as necessary by the MFEs.

CLI tool
========

Similar to ``fedx-scripts`` provided by ``frontend-build``, the unified library will provide a CLI tool.  This tool will expose commands to run the above webpack configurations, as well as other commands related to frontend projects.  The rationale and details of frontend projects and these CLI commands are the subject of a future ADR.

Consequences
************

This approach allows us to treat the "module MFE" architecture as an independent effort, as opposed to a set of features that need to fit in with the existing application MFE architecture.  We greatly reduce or eliminate the risk of regressions in libraries that the entire community relies on while we build a replacement which resembles and borrows code from them, but is otherwise significantly different.  This division makes it explicitly clear which features are used by the old architecture and which are used by the new one.

Further, it means we can migrate to the new architecture in a backwards compatible way by atomically migrating MFEs to use the new unified platform library, again, without running the risk of destabilizing existing libraries.

We believe this is a reasonable migration strategy given the generally low rate of feature development in these libraries.  We incur incrementally more maintenance burden in the interim while both sets of libraries exist, and a level of effort in absorbing any features or bug fixes in the existing libraries.  However, we believe that will be offset by an increase in development velocity for the new library, a clearer deprecation process, less risk of regressions in existing code, and an easier mental model of how the architecture is changing.

References
**********

* :doc:`OEP-65: Frontend Composability <../../oep-0065-arch-frontend-composability>`

Change History
**************

2024-06-26
==========

* Document created
* `Pull request #598 <https://github.com/openedx/open-edx-proposals/pull/598>`_
