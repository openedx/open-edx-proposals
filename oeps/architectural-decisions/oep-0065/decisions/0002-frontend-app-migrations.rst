Frontend App Migrations
#######################

Status
******

Accepted

Summary
*******

This ADR describes a migration path and simplified repository organization for the frontend-app-* repositories that is compatible with the shell provided by frontend-base, module federation, and frontend projects.

Context
*******

OEP-65 proposes adopting webpack module federation for Open edX micro-frontends (MFEs) as a means to enable *runtime module loading* and *shared dependencies* in the Open edX frontend.  The OEP lays out a series of changes necessary to enable these capabilities, which it refers to as building a "reference implementation" of runtime module loading and shared dependencies.

This reference implementation is effectively a new underlying architecture for our frontend. This ADR refers to this new architecture as the "module" architecture, as opposed to the historical "micro-frontend" architecture that has existed prior to OEP-65.

As part of this paradigm shift, our ``frontend-app-*`` repositories (MFEs) will need to be migrated to work with `frontend-base <https://github.com/openedx/frontend-base>`_.  Of particular note, this will require the repositories to:

* Adopt a new set of build/development CLI helpers
* Use the shell to provide the header, footer, and runtime initialization code, amongst other things.
* Organize their code into loosely-coupled top-level components, which we'll call *application modules*.

As we adopt ``frontend-base``, the libraries it replaces will undergo their own deprecation processes, which will need to coordinate with the migration of micro-frontends included in Open edX releases.  After that deprecation, the micro-frontend architecture will cease to be supported.

Decision
********

Each of our ``frontend-app-*`` repositories will migrate from being an independent "micro-frontend application" to being a library of modules that can be loaded into a common Shell, deployed as a Site.  These are called *application module libraries*.  We will document the migration process in detail.  At a high level, this will involve the following changes.

New Deployment Methods
======================

The application module libraries will be buildable in several different ways.

* Built as an independent Site using the Shell for initialization, the header and footer, configuration, and other foundational services (logging, analytics, i18n, etc.)
* Built as federated modules to be loaded into the Shell at runtime via webpack module federation.
* Built and released as an NPM package for build-time inclusion in a frontend Project, perhaps alongside other application modules from other libraries.

Environment Agnostic
====================

The application module libraries will no longer contain ``.env`` or ``env.config`` files for any specific environment, including Devstack and Tutor. Config filename patterns will be added to the ``.gitignore`` file.  They will continue to support adding a (git ignored) config file into the repository to build or develop it, but we also expect operators to use Projects and check their config files into those project repositories as their primary way of working with the application module libraries.

Projects and these config files will be the subject of a subsequent ADR.

Removed Dependencies
====================

Application module libraries will cease to use the following libraries in favor of ``frontend-base``:

* @openedx/frontend-build
* @edx/frontend-plaform
* @openedx/frontend-plugin-framework
* @edx/frontend-component-header
* @edx/frontend-component-footer
* @openedx/frontend-slot-footer
* @edx/brand
* core-js
* regenerator-runtime

Peer Dependencies
=================

We expect application module libraries to be dependencies of Frontend Projects by default for most operators.  Because of this, the following dependencies will become peer dependencies in the application module libraries themselves:

* @openedx/frontend-base
* @openedx/paragon
* react
* react-dom
* react-redux
* react-router
* react-router-dom
* redux

New CLI Tools
=============

The ``fedx-scripts`` CLI tools from ``frontend-build`` will be replaced with the ``openedx`` CLI tools from ``frontend-base``.  We'll discuss some of them in detail here, as they help illustrate what the library will be able to do:

* ``dev`` will start a dev server, loading the repository's application modules into the shell in a site.
* ``dev:module`` will start a dev server that provides the application modules via module federation.
* ``build`` will create a standalone deployable artifact that uses the shell (similar to the micro-frontend architecture)
* ``build:module`` will create a standalone deployable artifact that provides the application modules via module federation.
* ``release`` will package the library for distribution on npm.
* ``serve`` will work with ``build`` or ``build:module`` to locally serve the production assets they generated.
* ``pack`` will work with ``release`` to create a ``.tgz`` file suitable for installing in local git checkouts that depend on the library.  (this is a development tool)

The ``dev``, ``dev:module``, ``build``, and ``build:module`` CLI commands will rely on the existence of a "Site Config" file (the replacement for .env/env.config files) which will not be checked into the repository.

Distributed as NPM Packages
===========================

``frontend-app-*`` repositories that are part of Open edX releases will be expected to be published on NPM as a library which exports its modules.  These libraries will primarily be consumed by Frontend Projects.

Consequences
************

As the module architecture stabilizes, ``frontend-app-*`` maintainers and developers will be encouraged to migrate their micro-frontends into application module libraries, and to adopt the module architecture provided by ``frontend-base``.  (There will be a migration guide.)

As micro-frontends are migrated to application module libraries using the shell, there will be a deployment approach that mimics the micro-frontend architecture, but which will require operators to adopt a new underlying configuration and build process to achieve a similar result.  Each ``frontend-app-*`` repository will need a deprecation process for the micro-frontend configuration and build infrastructure.

Thinking in Modules
===================

Our definition of "module" aligns with the `industry standard definition <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules>`_.  It is also used in the context of `module federation <https://module-federation.io>`_. It's a self-contained part of the frontend that represents a specific part of the Site, and can be loaded in a variety of ways.  We have several sub-types of module:

* An *application module* represents a well-bounded sub-area of the Open edX frontend at a particular path.  This might be "courseware", "the login page", or "account settings". There are a number of application modules that are *required* for a functioning Open edX frontend Site.
* A "plugin module" represents an optional UI component that is generally added somewhere in an application module, or in the shell.  The header and footer, for instance, would be overridden with alternate implementations via plugin modules.  New tabs added to the course homepage are also plugin modules.
* *Service modules* which act as implementations of the logging or analytics services.
* *Script modules* which allow attaching arbitrary scripts to the page.

Our ``frontend-app-*`` repositories go from being "micro-frontend applications" to being a collection of application modules centered around a particular domain (learning, authoring, authn, etc.)  The question of which application modules belong in which repositories, and where the right boundaries are, is beyond the scope of this ADR.

Unsupported Customizations
==========================

The micro-frontend architecture took an extreme approach to "flexibility", allowing MFEs to diverge from each other in a variety of ways as described in OEP-65.  As a result, in the process of migrating them to the module architecture, there could be unforeseen refactoring that may need to happen in some MFEs that don't map into modules well, or which have customizations that aren't supported by the Shell.  While we hope to provide enough extensibility mechanisms to reduce the need for forking or hacky customizations, there will be customizations we haven't anticipated, which the community will need to work around or find ways to support.

Consistent Dependency Versions
==============================

Addressing our *lack* of dependency version consistency is one of the primary drivers of OEP-65.

The shell will support specific versions of shared dependencies (such as React, Paragon, or React Router).  All applications loaded into the shell's Site will be expected to use (or at least be compatible) with that version.  We intend to create lock-step version consistency of shared dependencies across all applications in the platform.  We envision each Open edX release supporting a particular major version of each shared dependency.

References
**********

* :doc:`OEP-65: Frontend Composability <../../oep-0065-arch-frontend-composability>`
* :doc:`OEP-65 Frontend Glossary <./frontend-glossary>`
* :doc:`ADR-0001: Unified Platform Repository <./0001-unified-platform-repository>`

Change History
**************

2024-08-28
==========

* Document created
* `Pull request #626 <https://github.com/openedx/open-edx-proposals/pull/626>`_
