OEP-65: Frontend Composability
##############################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-65 <oep-0065-arch-frontend-composability>`
   * - Title
     - Frontend Composability
   * - Last Modified
     - 2024-04-03
   * - Authors
     -
       * Adolfo R. Brandes <adolfo@axim.org>
       * Pedro Martello <pedro@hammerlabs.net>
       * David Joy <david@joy.engineering>
   * - Arbiter
     - Adam Stankiewicz <astankiewicz@2u.com>
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2024-04-03
   * - Review Period
     - <start - target end dates for review>
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     -
       * `FC-0054 - Composable Micro-frontends Discovery <https://openedx.atlassian.net/wiki/spaces/COMM/pages/4063821827/FC-0054+-+Composable+Micro-frontends+Piral+Discovery>`_
       * `FC-0007 - Modular MFE Domains Discovery <https://openedx.atlassian.net/wiki/spaces/COMM/pages/3614900241/CLOSED+FC-0007+-+Modular+MFE+Domains+Discovery>`_


Abstract
********

This OEP proposes that Open edX micro-frontends (MFEs) adopt module federation as an approach to address inconsistencies, performance issues, and inflexibilities inherent in the current MFE architecture.

Motivation
**********

Micro-frontends were originally designed to avoid some of the limitations of the monolithic ``edx-platform`` frontend, namely that otherwise independent teams were beholden to the build, test, and release lifecycle of the rest of the codebase. This dramatically slowed down the pace of frontend feature development, experimentation, and innovation.

As a result, the architecture focused on creating an unopinionated, loosely coupled set of applications with the goal of enabling teams to iterate quickly and independently. This goal was successful.

However, now roughly 5 years into this gradual re-platforming, we've discovered that the completely siloed nature of these MFEs has created its own set of problems. These problems are described below.

Consistency
===========

Because each MFE is completely independent, this can create inconsistencies from MFE to MFE.

Reusable components (Paragon)
-----------------------------

MFEs may use different versions of ``@openedx/paragon``, resulting in functional and stylistic differences.

Header/Footer
-------------

MFEs may use different versions of ``@edx/frontend-component-header`` and ``@edx/frontend-component-footer``, also resulting in functional, stylistic, and content/navigation differences.

Branding
--------

Brand packages created from ``@edx/brand-openedx`` may be different versions, resulting in any number of subtle visual differences.

Other Dependencies
------------------

MFEs may have completely different versions of any other dependency. We mitigate some of this by consolidating some important dependencies in ``@edx/frontend-build`` and ``@edx/frontend-platform``, but even those can have different versions from MFE to MFE. For developers, this increases cognitive load and slows velocity because of the need to adjust to the idiosyncracies of each application.

User and Developer Experience
=============================

Bundle Size
-----------

Each MFE bundles all of its own dependencies, even if they're the same version as another MFE. This means that as a user navigates between MFEs, they end up downloading the same set of common dependencies over and over again. This can result in megabytes of overhead for the average user navigating between a few MFEs, and slows down the entire experience.

Full Page Refreshes
-------------------

Each MFE has its own index.html page, and needs to load all its own dependencies from scratch whenever you navigate to it. This means that the browser performs a full page refresh each time a user transfers from MFE to MFE.

Build time
----------

Each MFE must bundle all its own dependencies at build time, creating significant overhead for the Webpack build process. When building multiple MFEs, this additional, repeated overhead adds up quickly, making builds prohibitively slow.

Dependency Maintenance
----------------------

Since each MFE has its own complete set of dependencies, the overhead of keeping them all up to date can be overwhelming. Security patches, bug fixes, new features, and breaking changes all add up and create a significant maintenance burden.

Composability
=============

We have no clean, low-overhead way of composing components from multiple MFEs into a single page, or extending an MFE with additional functionality.

The reality of MFEs is that while each application may represent a cohesive domain or bounded context, sometimes you need content and functionality from more than one domain on the page at the same time.

Alternately, providers may want to show different _versions_ of MFEs to different users while keeping the rest of the app (header, navigation, other MFEs) unchanged.

We aren't without options here, but what we have today has its own issues. It's worth walking through what we have today.

Shared Libraries (Current Option #1)
------------------------------------

Because each MFE is siloed from each other - both in repositories and at runtime - our only viable mechanism for sharing code has historically been to extract it into a shared library that gets bundled with each at build time. This creates more repository/dependency overhead.

Build-time package overrides (Current Option #2)
------------------------------------------------

NPM and package.json allow operators to override dependency resolution by installing an alternate version of a dependency prior to build time. This has historically been how we've allowed operators to override the header, footer, and brand.

The system is confusing and somewhat brittle and only works at build time. If an operator needs different headers/footers/brands for different clients, we've just multiplied the number of builds they need to do (already a problem even without this - see the "Build time" issue above!)

Frontend Plugins (Current Option #3)
------------------------------------

With the creation of the `frontend-plugin-framework <https://github.com/openedx/frontend-plugin-framework>`_, we're starting to have the capability to share components across MFEs as plugins, either at build time (direct plugins) or runtime (iframe plugins)

Direct plugins create some flexibility, but couple our repositories' builds together. Iframes are good for sandboxing and isolating code, but they're a very inefficient way to compose a UI, especially given the consistency and UX/DX concerns raised above. In a way, they exacerbate the problem even more. There's no way to do direct plugins via the MFE config API, since they rely on importing modules directly into the build.

Specification
*************

Fundamentally, enabling runtime module loading and shared dependencies address the majority of these problems, so our approach centers on enabling these two things.

Runtime Module Loading
======================

The capability to lazily load content from independently built modules into the page - without iframes - solves a few of the issues mentioned above. In particular, it gives us a way of composing UI elements from different MFEs/Domains dynamically at runtime without a "host" application needing to know anything about the "guest" at build-time. The two remain completely decoupled, save some shared runtime configuration.

This capability has a high degree of overlap with the goals of the `frontend-plugin-framework <https://github.com/openedx/frontend-plugin-framework>`_ (FPF), and was the inspiration and approach for the original POC for that framework. We expect that the FPF would add a new plugin type - probably "module plugin" - which handles loading plugins via runtime module loading.

Runtime module loading advantages
---------------------------------

- Reduces the frequency of full page refreshes. MFEs today are completely independent, so navigating between them means loading a completely new page (even if they share dependencies).
- Improves composability across MFEs/domains. We have no way to show more than one MFE on the same page today except by using iframes or by creating hard dependencies between MFEs at build-time by extracting 'shared' code into a new library, like `frontend-component-header <https://github.com/openedx/frontend-component-header>`_, `frontend-component-footer <https://github.com/openedx/frontend-component-footer>`_, or `frontend-lib-content-components <https://github.com/openedx/frontend-lib-content-components>`_, which increases our dependency maintenance burden significantly.
- Improves runtime extensiblity by allowing us to configure where an MFE's code should be loaded from, rather than needing to build it in to an app. It dovetails nicely with the FPF by providing us with a seamless, performant, and flexible way of extending our frontends without needing to rebuild the host.

Shared Dependencies
===================

Sharing dependencies between MFEs at runtime compliments runtime module loading.

As noted above, MFEs have a number of dependencies which are common between them, but which aren't actually shared at runtime in any way. The capability to share these dependencies - such as ``react``, ``@openedx/paragon``, etc. - would mitigate a great deal of our `Consistency`_ and `User and Developer Experience`_ issues.

We expect the following packages - which are used in the vast majority of MFEs today - could all be shared between MFEs.

.. list-table::
   :widths: 30 20 50

   * - **Package**
     - **Estimated Size**
     - **Notes**
   * - @edx/brand
     - Variable
     -
   * - @edx/frontend-component-footer
     - 88.1k
     -
   * - @edx/frontend-component-header
     - 156.9k
     -
   * - @edx/frontend-platform
     - 355.3k
     -
   * - @openedx/paragon (or @edx/paragon)
     - ~950k
     - bundlephobia.com threw an error on the Paragon build, but we expect it's the largest package here.
   * - classnames
     - 0.8k
     -
   * - core-js
     - 241.1k
     -
   * - prop-types
     - 0.9k
     -
   * - react
     - 6.4k
     -
   * - react-dom
     - 130.2k
     -
   * - react-redux
     - 11.2k
     -
   * - react-router
     - 58.9k
     -
   * - react-router-dom
     - 77.1k
     -
   * - redux
     - 3.7k
     - Ultimately should be replaced with @reduxjs/toolkit. This document isn't making a judgment on the utility of redux, just noting that it is currently in use in the vast majority of MFEs.
   * - regenerator-runtime
     - 6.6k
     -

Total size: ~2,087.2k unzipped

Shared dependency advantages
----------------------------

- Encourages runtime consistency across all types of dependencies. If our MFEs use the same version, we significantly reduce the cognitive load and context switching involved in working with multiple MFEs, cut down on visual inconsistencies at runtime, etc.
- Reduces runtime bundle size. We'll ship far less code to the client across a user's browsing session.

Caveat: "build time" and "dependency maintenance"
-------------------------------------------------

Note that "build time" and "dependency maintenance" are not mentioned in the advantages above.  The reasons for this have to do with how shared dependencies are resolved for modules at runtime, and are described in more detail in the `Module federation`_ section below.

At a high level, even if MFEs share dependencies, we want to preserve the ability for them to "fall back" to their own version of a shared dependency if a version already loaded on the page is incompatible with their own.  To do this, each MFE builds and deploys its own version of all its dependencies in case dependency resolution determines they're needed.

This means their build has to take time to bundle them, and the repository still needs the dependencies present in the package.json file.  Sharing dependencies doesn't help us much w/r/t build time and dependency maintenance.

Approach
========

We intend to enable runtime module loading and shared dependencies via `Webpack module federation <https://webpack.js.org/concepts/module-federation>`_. More information on module federation beyond its webpack implementation can be found on its `dedicated site at module-federation.io <https://module-federation.io/>`_.

Further, we believe that we also need an approach to maintaining and enforcing dependency consistency across MFEs to realize the benefits of shared dependencies. MFEs need a compatible version of the shared dependency to be available, otherwise they need to fall back to their own version. The process, tooling, and/or code organization necessary to provide that consistency is not something module federation can help with and needs to be addressed separately.

Module Federation
-----------------

Module federation is implemented as a `plugin for Webpack <https://webpack.js.org/plugins/module-federation-plugin/>`_ that enables micro-frontends to be composed into the same page at runtime even if they're built separately and independently deployed. The pieces being composed are "modules". It lets us configure which dependencies should be shared between modules on a page and what modules a particular frontend exposes to be loaded by other frontends.

If two modules require incompatible versions of a shared dependency, the second one loaded can fall back to loading a version it's compatible with from its own build. (see the link in step #4 below for details.)

In terms of Open edX MFEs, this means:

1. MFEs can continue to be built independently.
2. The Webpack build will include a manifest of which sub-modules the MFE provides at runtime.
3. ``@edx/frontend-build`` will specify - through its Webpack configs - a common set of shared dependencies to be used at runtime by all MFEs.
4. Webpack will intelligently resolve those dependencies at runtime, `taking into account each module's specific version requirements <https://www.angulararchitects.io/en/blog/getting-out-of-version-mismatch-hell-with-module-federation>`_.
5. MFEs can dynamically load modules from other MFEs at runtime with Webpack handling hooking them up to the right dependencies.

Because we already use Webpack, the work to add the ModuleFederationPlugin to our configurations is small and uninvasive (see proof of concept in the `Reference Implementation`_ section below).

Maintaining Dependency Consistency
----------------------------------

This proposal fundamentally changes how we work with MFE dependencies, and will require us to adopt a more rigorous approach to ensuring dependency consistency and compatiblity. That consistency doesn't come for free just by adopting shared dependencies at runtime: independent codebases will need to be kept in sync.

We expect that this may need to take a number of possible forms.

Process
^^^^^^^

We need to ensure maintainers and developers know what dependency versions to use, and when they need to upgrade to stay consistent. Open edX release documentation should include documentation of which frontend dependency versions are compatible with the release, likely pinned to a major version (i.e., React 17.x, Paragon 22.x, etc.)

We also need a process to migrate core repositories through breaking changes in third-party dependencies. Ideally follow the `Upgrade Project Runbook <https://openedx.atlassian.net/wiki/spaces/AC/pages/3660316693/Upgrade+Project+Runbook>`_.

Best Practices
^^^^^^^^^^^^^^

We need to ensure we minimize breaking changes in our own libraries (such as Paragon, the header, footer, frontend-platform, frontend-build, etc.) We suggest accomplishing this by:

- Creating new versions of components with breaking changes (``ButtonV2``, ``webpack.dev.config.v2.js``) rather than modifying existing ones.
- Leveraging the DEPR process for communication and removing old component versions
- Aligning that removal and the subsequent breaking changes with Open edX releases, and documenting it in their release notes.

Further, we could reduce the overhead of dependency maintenance and ensure MFEs stay up to date by pinning dependencies to major versions using ``^`` on versions in our package.json files.

Tooling
^^^^^^^

Maintainers and developers should be warned of incompatibilities created by their PRs, or outside the repository by another project (such as the shell).

This could take the form of Github tooling which notifies maintainers and developers that their frontend code has:

1. Drifted behind the compatible version of a shared dependency for a given Open edX release or the main branch.
2. Has upgraded beyond what is compatible with a given Open edX release or the main branch.

Code Organization
^^^^^^^^^^^^^^^^^

We propose creating a passthrough library of shared dependencies. MFEs would depend on this single library in its package.json rather than on individual shared dependencies. This library would be versioned in accordance with the `Best Practices`_ suggestions above, meaning that breaking changes would be minimized and dependencies would be pinned to major versions via ``^`` on version numbers. Its version manifest would be the source of the version numbers for the `Process`_ and `Tooling`_ suggestions.

To minimize impact on our MFEs, this library may need to be supported with some build-time configuration in Webpack that aliases its passthrough imports to their original package names. This would let us continue to write:

  ``import React from 'react';``

Instead of having to write something like:

  ``import React from '@openedx/<passthrough library name>/react';``

or:

  ``import { React } from '@openedx/<passthrough library name>';``

Out of Scope
============

There are a few important - but tangental - concerns which are considered out of scope for this OEP and its resulting reference implementation.

- Implementation details of how module federation would be added in the frontend-plugin-framework.
- How Tutor and other distributions will need to change to adopt module federation.
- Opinions on which dependencies we should adopt going forward (such as redux or other state management solutions)

Rationale
*********

The majority of the concerns expressed in the Motivation section revolve around a lack of shared dependencies and the way in which MFEs are currently siloed from each other, preventing us from creating a more seamless, cohesive experience.

Module federation specifically addresses these use cases exactly. It's right-sized to the problem at hand, can be accomplished with a minimum of impact on our existing MFEs, and can be done in a backwards compatible way (more on that below).

An approach to maintaining dependency consistency is essential to realize the benefits of sharing dependencies, or we've accomplished very little even though we've added the capability. An approach to providing this consistency is not a prerequisite for implementing module federation, to be clear, but the `success` of module federation is tightly coupled to it.

Backward Compatibility
**********************

We intend to maintain backwards compatibility while migrating to using module federation. We can do this by creating a separate set of Webpack configurations in ``@edx/frontend-build`` and separate build targets in converted MFEs; the footprint of module federation on "guest" MFEs is very small, requiring virtually no code changes in the application itself, and a few additional configuration options in the MFE's Webpack config to identify exposed components.

Ultimately MFEs will no longer be responsible for initializing frontend-platform or rendering the header and footer. We will follow the DEPR process for retiring this code in MFEs once (and if) we make the module federation architecture required.

In the interim, MFEs will have both a webpack configuration that exposes modules for consumption by other hosts as alternate entry points (to use Webpack parlance) _and_ the primary entry point which initializes frontend platform and loads the header/footer. Early POC testing indicates this won't be a problem.

Reference Implementation
************************

A proof of concept has already been created demonstrating how Webpack module federation works with two Open edX micro-frontends based on the frontend-template-application. The POC has several shared libraries (``react``, ``react-dom``, and ``@openedx/paragon``), and loads a React component module from a guest MFE into the page of a host MFE. It supports hot module replacement during development, and runs on the two MFEs' `webpack-dev-server` instances.

The POC repositories can be found here:

- `frontend-app-mf-host <https://github.com/davidjoy/frontend-app-mf-host>`_
- `frontend-app-mf-guest <https://github.com/davidjoy/frontend-app-mf-guest>`_

Proposed MFE Architecture
=========================

MFEs and Modules
----------------

Each of our MFEs will export a set of one or more modules that can be loaded by other MFEs. For instance, ``frontend-app-profile`` would likely export the ``ProfilePage`` component. Other MFEs may export their own pages, or perhaps plugins/widgets/components to be loaded by the `frontend-plugin-framework <https://github.com/openedx/frontend-plugin-framework>`_ via a "module" plugin type based on this implementation.

Hosts and Guests
----------------

MFEs become either "hosts" or "guests" or both. A host is an MFE that loads runtime modules from a guest. A guest may itself act as a host to modules from another guest.

Shell MFE
---------

We will create a new "shell" MFE to act as the top-level host for all other MFEs. It is exclusively responsible for:

- Initializing the application via ``@edx/frontend-platform``.
- Loading the base, expected version of all our shared dependencies.
- Rendering the "layout" of the application, including the header and footer.
- Loading the brand.

Like other hosts, it is also responsible for:

- Loading all the manifests from the "guest" MFEs it intends to load.
- Using module federation to load the guest MFEs on demand.

Guest MFEs (not the shell)
--------------------------

Guest MFEs that require a version of a shared dependency that's incompatible with the shell's version may load their own, provided that dependency isn't a singleton (like ``react`` or ``@edx/frontend-platform``). Singletons may only be loaded once because they break if they're loaded more than once on a single page.

Guests loading their own versions of shared dependencies degrades the performance and experience of end users. MFE authors should endeavor to use dependencies compatible with the version loaded by the shell. If we use a passthrough library of shared dependencies, this becomes easier.

Converting the POC to a reference implementation
================================================

To convert this POC into a reference implementation, we need to minimally:

- Create a new "shell" micro-frontend to be the top-level "host" for all our other micro-frontends.
- Create module federation-based development and production Webpack configurations in ``@edx/frontend-build``.
- Modify the Webpack configuration to share the complete list of shared dependencies from the shell.
- Pick an existing MFE (or two) to convert to use module federation. Add build targets to these "guest" micro-frontends that can be used to build them in module-federation mode.
- Extend the Webpack configuration in the MFEs by defining what modules each "guest" MFE exports.  We suggest that the package.json `exports <https://nodejs.org/api/packages.html#subpath-exports>`_ field be used to codify this list of exports, and that Webpack pull it in from package.json to configure ``ModuleFederationPlugin``.  The format appears to be the same.
- Give "guest" MFEs a way of seeing their own config, since they'll be getting ``@edx/frontend-platform`` as a shared dependency from the shell, and won't be initializing it themselves.

Secondary concerns include:

- Ensuring nested dynamic modules work correctly.
- Ensuring static assets load properly in guest modules.
- DEPR process around the migration.
- Documentation on how to convert an MFE to use module federation and the shell.
- The default configuration for loading "core" MFEs.
- Documentation on how to do development
- A decision on whether we use the MFE config API, env.config.js, both, or something else to supply the module federation configuration, whether it's one big combined document or whether each MFE has its own.
- How we sandbox and put error boundaries around dynamically loaded modules.
- Whether we create a central global state store, and whether that's redux.
- Whether we need an eventing system for inter-MFE communication.
- How we manage breaking dependency changes across MFEs.

Rejected Alternatives
*********************

Piral
=====

A prior iteration of this OEP and discovery effort (FC-0007) came to the conclusion that we should adopt Piral, a comprehensive micro-frontend web framework, to address our concerns with the Open edX micro-frontend architecture.

After further investigation and review of our stated pains, observed deficiencies, hopes, and vision for Open edx micro-frontends, we chose to adjust course away from Piral. Piral solves runtime module loading and shared dependencies in a similar way to Webpack module federation - and can in fact use it internally - but does so in a more proprietary, opinionated, and opaque way, adding additional layers/wrappers around it. While Webpack is one of many bundlers available, it's also the defacto standard against which others are judged, and has wide industry adoption. Webpack module federation is a standard approach for composing frontends.

Piral is an impressive piece of software, built primarily by one individual, trying to solve a much broader problem than we have. Because of this, it brings along with it a great deal of complexity that we don't need and already have solutions for. Piral aims to be a complete toolkit for building web applications, including authentication, plugins, its own global state mechanism, extensions that provide ready-made UI components, etc.

We need a mechanism to provide shared dependencies and composable frontends that can fit in with our existing ecosystem. Adopting Piral would likely involve significant refactoring of existing MFEs to fit into its framework and to turn them into "pilets", which locks us in to the Piral way of doing things.

It feels like our needs more closely align with the narrower scope of module federation, and that it's a more right-sized solution to our architectural problems.

Combining MFEs into 2-3 monoliths
=================================

Folding our micro-frontends together into a few larger frontends (LMS and Studio, for instance) solves our need for shared dependencies in a different way - it just shares all the code so there's one set of dependencies for all of it. We could continue to rely on frontend-plugin-framework for cross-domain plugins, but "plugins" within the larger domain become a simple import from another part of the application.

This approach was abandoned because we still believe that MFE independence is a core need for our platform, and we can't go back to a few monolithic frontends.

Combining MFEs into a monorepo
==============================

A monorepo would co-locate all of our core MFEs and libraries (``@openedx/paragon``, ``@edx/frontend-platform``, ``@edx/frontend-component-header``, ``@edx/frontend-component-footer``, etc.) in the same repository, but maintain their independent release and deployment cycles. We believe this would help us more readily keep consistent dependency versions across MFEs. But it would also introduce a layer of complexity to our code organization and be a highly invasive way of solving our dependency consistency issues, as we'd have to move all of our core frontend code into a new repository.

Further, it wouldn't solve our consistency problems for anyone working with a non-core MFE or library. We want to create parity between the process for core and non-core repositories to ensure our approach is serving everyone's needs, not just maintainers of core repositories.

We acknowledge that there are benefits here, but believe that it's more work than it's worth, is only a partial solution, and we have less complex options available to us.

Doing Nothing
=============

We feel that the siloing of micro-frontends, the proliferation of dependencies, the difficulty of extending our platform, and the toil of ongoing maintenance is untenable. This requires us to act to improve the approachability of our frontend architecture; it's not good enough yet.

Appendix A: How Module Federation Works
***************************************

Without reproducing the details of Webpack's module federation documentation, effectively the following steps occur:

- A host application is provided a list of remote entry points, either through build-time configuration or via an API request to a runtime configuration service (like the MFE config API).
- Each guest application has a "remote entry" JavaScript file which acts as a manifest of the modules that application exposes, each of which is its own JavaScript file. The guest's versions of all of its dependencies are also present in the manifest.
- The host application loads those remote entry points as scripts to the document's <head> tag for later use.
- To load a module, the host application loads the module's JavaScript file based on the URL in the remote entry file, and Webpack's runtime provides that module with any shared dependencies already loaded in the host application.
- If the Webpack runtime can't reconcile the version of a dependency needed by a guest module with those already loaded, it will use the remote entry manifest to load the guest's version as well.

Change History
**************

2024-04-03
==========

* Document created

2024-04-04
==========

* `Pull request #575 <https://github.com/openedx/open-edx-proposals/pull/575>`_
* Adding an arbiter.
* Light editing for punctuation and clarity
* Adding another use case for composability.
* Adding build-time package overrides as a composability option.
* Adding more details to the reference implementation section.

2024-04-09
==========

* Rewriting and clarifying the Specification section. Simplifying language around Webpack module federation and adding a variety of links out to external resources.
* Adding specific recommendations for Maintaining Dependency Consistency.  Also adding it to the Rationale.
* Rewriting the section on why "build time" and "dependency maintenance" aren't improved by adding shared dependencies.
* Adding monorepos to the Rejected Alternatives section.
* Adding a sub-section on Proposed MFE Architecture to the Reference Implementation section.
* Adding a link to the Upgrade Project Runbook.

2024-04-10
==========

* Adding references to the discovery projects for this OEP: FC-0054 and FC-0007.
