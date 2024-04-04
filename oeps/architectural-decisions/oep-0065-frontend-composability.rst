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
     - <Arbiter's real name and email address>
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
     - <links to any other relevant discussions or relevant related materials>

Abstract
********

This OEP proposes that Open edX micro-frontends (MFEs) adopt module federation as an approach to address inconsistencies, performance issues, and inflexibilities inherent in the current MFE architecture.

Motivation
**********

Micro-frontends were originally designed to avoid some of the limitations of the monolithic ``edx-platform`` frontend, namely that otherwise independent teams were beholden to the build, test, and release lifecycle of the rest of the codebase.  This dramatically slowed down the pace of frontend feature development, experimentation, and innovation.

As a result, the architecture focused on creating an unopinionated, loosely coupled set of applications with the goal of enabling teams to iterate quickly and independently.  This goal was successful.

However, now roughly 5 years into this gradual re-platforming, we've discovered that the completely siloed nature of these MFEs has created its own set of problems.  These are described below.

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

MFEs may have completely different versions of any other dependency.  We mitigate some of this by consolidating some important dependencies in  ``@edx/frontend-build`` and ``@edx/frontend-platform``, but even those can have different versions from MFE to MFE.  For developers, this increases cognitive load and slows velocity because of the need to adjust to the idiosyncracies of each application.

User and Developer Experience
=============================

Bundle Size
-----------

Each MFE bundles all of its own dependencies, even if they're the same version as another MFE.  This means that as a user navigates between MFEs, they end up downloading the same set of common dependencies over and over again.  This can result in megabytes of overhead for the average user navigating between a few MFEs, and slows down the entire experience.

Full Page Refreshes
-------------------

Each MFE has its own index.html page, and needs to load all its own dependencies from scratch whenever you navigate to it.  This means that the browser performs a full page refresh each time a user transfers from MFE to MFE.

Build time
----------

Each MFE must bundle all its own dependencies at build time, creating significant overhead for the webpack build process.  When building multiple MFEs, this additional, repeated overhead becomes prohibitively onerous.

Dependency Maintenance
----------------------

Since each MFE has its own complete set of dependencies, the overhead of keeping them all up to date can be overwhelming.  Security patches, bug fixes, new features, and breaking changes all add up and create a significant maintenance burden.

Composability
=============

We have no clean, low-overhead way of composing components from multiple MFEs into a single page, or extending an MFE with additional functionality.

The reality of MFEs is that while each application may represent a cohesive domain or bounded context, sometimes you need content and functionality from more than one domain on the page at the same time.

Alternately, providers may want to show different MFEs to different users while keeping the rest of the app (header, navigation, other MFEs) unchanged.

Shared Libraries (Current Option #1)
------------------------------------

Because each MFE is siloed from each other - both in repositories and at runtime - our only viable mechanism for sharing code has historically been to extract it into a shared library that gets bundled with each at build time.  This creates more repository/dependency overhead.

Frontend Plugins (Current Option #2)
-----------------------------------

With the creation of the `frontend-plugin-framework <https://github.com/openedx/frontend-plugin-framework>`_, we're starting to have the capability to share components across MFEs as plugins, either at build time (direct plugins) or runtime (iframe plugins)

Direct plugins create some flexibility, but couple our repositories' builds together. Iframes are good for sandboxing and isolating code, but they're a very inefficient way to compose a UI, especially given the consistency and UX/DX concerns raised above.  In a way, they exacerbate the problem even more.

Specification
*************

Fundamentally, runtime module loading and shared dependencies address the majority of these problems.  Both are core capabilities of module federation, and its primary value proposition. Module federation is an advanced technique designed specifically for use cases like ours, and encompases a few related ideas.  It can be accomplished manually by leveraging webpack externals, via webpack module federation, or other similar mechanisms in other bundlers/module loaders, such as rspack and System.js.

Runtime Module Loading
======================

The capability to lazily load content from a different, independently built MFE into the page - without iframes - solves a few of the issues mentioned above.  In particular, it gives us a way of composing UI elements from different MFEs/Domains dynamically at runtime without a "host" application needing to know anything about the "guest" at build time.  The two remain completely decoupled, save some shared runtime configuration.

This capability has a high degree of overlap with the goals of the frontend-plugin-framework (FPF), and was the inspiration and approach for the original POC for that framework.  We expect that the FPF would add a new plugin type - probably "module plugin" - which handles loading plugins via webpack module federation.

Runtime module loading improves:

- Frequency of full page refreshes
- Composability across MFEs/domains
- Runtime extensiblity

It also helps preserve the independent deployability and autonomy of our MFEs.

Shared Dependencies
===================

Going along with runtime module loading is the ability to share dependencies between MFEs.  This is another core capability of webpack module federation.

As noted above, MFEs have a number of dependencies which are common between them, but which aren't actually shared at runtime in any way.   The capability to share these dependencies - such as ``react``, ``@openedx/paragon``, etc. - would mitigate a great deal of our inconsistency and UX/DX issues.

We expect the following packages - which are in use in the vast majority of MFEs today - could all be shared between MFEs.

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
     - Big
     - bundlephobia.com choked on the paragon build, but we expect it’s the largest thing here.  That said, today, Paragon components are dropped during treeshaking
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
     - Ultimately should be replaced with @reduxjs/toolkit.  This document isn't making a judgment on the utility of redux, just noting that it is currently in use in the vast majority of MFEs.
   * - regenerator-runtime
     - 6.6k
     -

Total size: ~892.2k unzipped

Sharing these dependencies has several important positive effects:

- Enforces consistency across all types of dependencies
- Reduces runtime bundle size

Note that "build time" and "dependency maintenance" are not mentioned above.

Interestingly, because of the way webpack module federation's dependency sharing is implemented, this may not have a large impact on build time or dependency maintenance, as "guest" MFEs still need to have the dependencies installed for the build to succeed, and artifacts for them will still be created - they just won't be used at runtime.  Using webpack externals instead of module federation ensures that the dependencies won't be bundled/part of the build, but they still need to be dev dependencies in the repository for IDE hinting, auto completion, linting, etc.

Enforcing Dependency Consistency
================================

This proposal fundamentally changes how we work with MFE dependencies, and will require us to adopt a more rigorous approach to ensuring dependency consistency and compatiblity.

We expect that this may need to take a number of possible forms:

- Process to ensure maintainers and developers know what dependency versions to use, and when they need to upgrade to stay consistent.
- Process to migrate through breaking changes in third-party dependencies.
- Best practices to ensure we minimize breaking changes in our own libraries (such as Paragon).
- Tooling to warn maintainers/developers of inconsistencies.
- A more drastic code organization scheme, such as a monorepo, which provides shared dependencies to core applications and libraries via a common package.json file.
- A new, versioned shared library which provides shared dependencies to MFE repositories as a single dependency (TBD how that would work)

One way or another, we're abandoning some of the independence of MFEs as originally envisioned years ago, in favor of more consistency between them, and we'll need a way to manage that.

Out of Scope
============

There are various aspects of this which are considered out of scope for this OEP.

- Opinions on which dependencies we should adopt going forward (such as redux or other state management solutions)
- Implementation details of how module federation would be added in the frontend-plugin-framework.
- Details of a monorepo approach.
- How we will enforce dependency consistency.
- How Tutor and other distributions will need to change to adopt module federation.

Rationale
*********

The majority of the concerns expressed in the Motivation section revolve around a lack of shared dependencies and the way in which MFEs are currently siloed from each other, preventing us from creating a more seamless, cohesive experience.

Module federation specifically addresses these use cases exactly.  It's right-sized to the problem at hand, can be accomplished with a minimum of impact on our existing MFEs, and can be done in a backwards compatible way (more on that below).

Backward Compatibility
**********************

We intend to maintain backwards compatibility while migrating to using module federation.  We can do this by creating a separate set of webpack configurations in ``@edx/frontend-build`` and separate build targets in converted MFEs; the footprint of module federation on "guest" MFEs is very small, requiring virtually no code changes in the application itself, and a few additional configuration options in the MFE's webpack config to identify exposed components.

Reference Implementation
************************

A proof of concept has already been created demonstrating how webpack module federation works with two Open edX micro-frontends based on the frontend-template-application.  The POC has several shared libraries (``react``, ``react-dom``, and ``@openedx/paragon``), and loads a React component module from a guest MFE into the page of a host MFE.  It supports hot module replacement during development, and runs on the two MFEs' webpack-dev-servers.

To convert this into a reference implementation, we need to minimally:

- Create a new "shell" micro-frontend to be the top-level "host" for all our other micro-frontends.
- Modify the webpack configuration to share the complete list of shared dependencies from the shell.
- Create module federation-based development and production webpack configurations in ``@edx/frontend-build``.
- Pick an existing MFE (or two) to convert to use module federation.  Add build targets to these "guest" micro-frontends that can be used to build them in module-federation mode.
- Extend the webpack configuration by defining what modules the "guest" MFEs expose.

Rejected Alternatives
*********************

Piral
=====

A prior iteration of this OEP and discovery effort (FC-0007) came to the conclusion that we should adopt Piral, a comprehensive micro-frontend web framework, to address our concerns with the Open edX micro-frontend architecture.

After further investigation and review of our stated pains, observed deficiencies, hopes, and vision for Open edx micro-frontends, we chose to adjust course away from Piral.  Piral solves runtime module loading and shared dependencies in a similar way to webpack module federation - and can in fact use it internally - but does so in a more proprietary, opinionated, and opaque way, adding additional layers/wrappers around it.  While webpack is one of many bundlers available, it's also the defacto standard against which others are judged, and has wide industry adoption.  Webpack module federation is a standard approach for composing frontends.

Piral is an impressive piece of software, built primarily by one individual, trying to solve a much broader problem than we have.  Because of this, it brings along with it a great deal of complexity that we don't need and already have solutions for.  Piral aims to be a complete toolkit for building web applications, including authentication, plugins, its own global state mechanism, extensions that provide ready-made UI components, etc.

We need a mechanism to provide shared dependencies and composable frontends that can fit in with our existing ecosystem.  Adopting Piral would likely involve significant refactoring of existing MFEs to fit into its framework and to turn them into "pilets", which locks us in to the Piral way of doing things.

It feels like our needs more closely align with the narrower scope of module federation, and that it's a more right-sized solution to our architectural problems.

Combining MFEs
==============

Folding our micro-frontends together into a few larger frontends (LMS and Studio, for instance) solves our need for shared dependencies in a different way - it just shares all the code so there's one set of dependencies for all of it.  We could continue to rely on frontend-plugin-framework for cross-domain plugins, but "plugins" within the larger domain become a simple import from another part of the application.

This approach was abandoned because we still believe that MFE independence is a core need for our platform, and we can't go back to a few monolithic frontends.

Doing Nothing
=============

We feel that the siloing of micro-frontends, the proliferation of dependencies, the difficulty of extending our platform, and the toil of ongoing maintenance is untenable.  This requires us to act to improve the approachability of our frontend architecture; it's not good enough yet.

Appendix A: How Module Federation Works
***************************************

Without reproducing the details of webpack's module federation documentation, effectively the following steps occur:

- A host application is provided a list of remote entry points, either through build-time configuration or via an API request to a runtime configuration service (like the MFE config API).
- Each guest application has a "remote entry" JavaScript file which acts as a manifest of the modules that application exposes, each of which is its own JavaScript file.  The guest's versions of all of its dependencies are also present in the manifest.
- The host application loads those remote entry points as scripts to the document's <head> tag for later use.
- To load a module, the host application loads the module's JavaScript file based on the URL in the remote entry file, and Webpack's runtime provides that module with any shared dependencies already loaded in the host application.
- If the webpack runtime can't reconcile the version of a dependency needed by a guest module with those already loaded, it will use the remote entry manifest to load the guest's version as well.


Change History
**************

2024-04-03
==========

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
