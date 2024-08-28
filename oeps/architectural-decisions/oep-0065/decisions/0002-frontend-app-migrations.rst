OEP-65 Frontend App Migrations
##############################

Status
******

Accepted

Summary
*******

This ADR describes a migration path and simplified repository organization for the frontend-app-* repositories that is compatible with the shell provided by frontend-base as well as module federation and frontend projects.

Context
*******

OEP-65 proposes adopting webpack module federation for Open edX micro-frontends (MFEs) as a means to enable *runtime module loading* and *shared dependencies* in the Open edX frontend.  The OEP lays out a series of changes necessary to enable these capabilities, which it refers to as building a "reference implementation" of runtime module loading and shared dependencies.

This reference implementation is effectively a new underlying architecture for our frontend. This ADR refers to this new architecture as the "module" architecture, as opposed to the historical "micro-frontend" architecture that has existed prior to OEP-65.

As part of this paradigm shift, our frontend-app-* repositories (MFEs) will need to be migrated to work with frontend-base.  Of particular note, this will require the repositories to:

- Adopt a new set of build/development CLI helpers
- Use the shell to provide the header, footer, and runtime initialization code, amongst other things.
- Organize their code into loosely-coupled top-level components, which we'll call *modules*.

As we adopt frontend-base, the libraries it replaces will undergo their own deprecation processes, which will need to coordinate with the migration of micro-frontends included in Open edX releases.  After that deprecation, the micro-frontend architecture will cease to be supported.

Decision
********

Each of our frontend-app-* repositories will migrate from being an independent "micro-frontend application" to being a library of modules that can be loaded into a common Shell, deployed as a Site.  We will document the migration process in detail.  At a high level:

- They will be deployable via a variety of methods, all of which will use the Shell.
- They will be deployable together or independently.
- They will no longer contain .env or env.config files for any environment, including Devstack and Tutor.
- They will cease to use the following libraries in favor of frontend-base:
  - @openedx/frontend-build
  - @edx/frontend-plaform
  - @openedx/frontend-plugin-framework
  - @edx/frontend-component-header
  - @edx/frontend-component-footer
  - @openedx/frontend-slot-footer
  - @edx/brand
  - core-js
  - regenerator-runtime
- The following dependencies will become peer dependencies:
  - @openedx/frontend-base
  - @openedx/paragon
  - react
  - react-dom
  - react-redux
  - react-router
  - react-router-dom
  - redux
- They will replace the frontend-build ``fedx-scripts`` CLI tools with the frontend-base ``openedx`` CLI tools.  We'll discuss some of them in detail here, as they help illustrate what the library will be able to do:
  - ``dev`` will start a dev server, loading the repository's application modules into the shell in a site.
  - ``dev:module`` will start a dev server that provides the application modules via module federation.
  - ``build`` will create a standalone deployable artifact that uses the shell (similar to the micro-frontend architecture)
  - ``build:module`` will create a standalone deployable artifact that provides the application modules via module federation.
  - ``release`` will package the library for distribution on npm.
  - ``serve`` will work with ``build`` or ``build:module`` to locally serve the production assets they generated.
  - ``pack`` will work with ``release`` to create a ``.tgz`` file suitable for installing in local git checkouts that depend on the library.  (this is a development tool)
- The ``dev``, ``dev:module``, ``build``, and ``build:module`` CLI commands will rely on the existence of a "Site Config" file (the replacement for .env/env.config files) which will not be checked into the repository.
- frontend-app-* repositories that are part of Open edX releases will be expected to be published on NPM as a library which exports its modules.

Redefining "application"
========================

This decision constitutes a re-definition of "application" in the Open edX frontend ecosystem.  In the past, "application" was synonymous with the entire frontend-app-* repository.  With this ADR, "application" will be synonymous with the top-level modules provided by a frontend-app-* repository. Those modules are applications.

We believe that the boundaries of our frontend-app-* repositories are fairly arbitrary and not as semantically meaningful as the boundaries of the *application modules* within them.  We want these modules to be the primary unit of modularity - not the entire repository.  The shell will be configured to load applications, not repositories.

As an example:

* The ``frontend-app-learning`` repository contains modules related to "learning" - course outline, courseware, the progress and dates pages, etc.
* The ``frontend-app-authn`` repository contains modules related to "authentication" - login, registration, logout, forgot password, etc.

The shell will be configured to load things like "courseware" or "login", not "learning" and "authn" - the repository the module is in is incidental.

Regarding "plugins"
-------------------

Plugins are another type of module.  We believe that they are different than application modules because they have a different interface with the shell.  Plugins are generally UI widgets loaded into a slot, though some can be larger.  Applications are generally large and complex, comprising multiple pages of functionality with internal routing.  They are also loaded at a path in the application, not in a plugin slot.

Modeling applications as Plugins is a possibility, but we believe that it feels like too much indirection and abstraction, and minimizes the importance of applications.  Without them there's no site, and many are likely *required* functionality for the Open edX frontend to function at all.

Some frontend-app-* repositories may also include Plugins, but they aren't the primary subject of this ADR.

Consequences
************

As the module architecture stabilizes, frontend-app-* maintainers and developers will be encouraged to migrate their micro-frontends into application module libraries, and to adopt the module architecture provided by frontend-base.  (There will be a migration guide.)

As micro-frontends are migrated application modules using the shell, there will be a deployment approach that mimics the micro-frontend architecture, but which will require operators to adopt a new underlying configuration and build process to achieve a similar result.  Each frontend-app-* repository will need a deprecation process for the micro-frontend configuration and build infrastructure.

Unsupported Customizations
==========================

The micro-frontend architecture took an extreme approach to "flexibility", allowing MFEs to diverge from each other in a variety of ways as described in OEP-65.  As a result, in the process of migrating them to the module architecture, there could be unforeseen refactoring that may need to happen in some MFEs that don't map into modules well, or which have customizations that aren't supported by the Shell.  While we hope to provide enough extensibility mechanisms to reduce the need for forking or hacky customizations, there will be customizations we haven't anticipated, which the community will need to work around or find ways to support.

Consistent Dependency Versions
==============================

The shell will support specific versions of shared dependencies (such as React, Paragon, or React Router).  All applications loaded into the shell's Site will be expected to use (or at least be compatible) with that version.  We intend to create lock-step version consistency of shared dependencies across all applications in the platform.  We envision each Open edX release supporting a particular major version of each shared dependency.

References
**********

* :doc:`OEP-65: Frontend Composability <../../oep-0065-arch-frontend-composability>`
* :doc:`ADR-0001: Unified Platform Repository <./0001-unified-platform-repository>`

Change History
**************

2024-08-28
==========

* Document created
* `Pull request #626 <https://github.com/openedx/open-edx-proposals/pull/626>`_
