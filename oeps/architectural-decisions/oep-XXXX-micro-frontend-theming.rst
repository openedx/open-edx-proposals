======================================
OEP-XXXX: Micro-frontend Customization
======================================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-XXXX <oep-XXXX-arch-micro-frontend-theming>`
   * - Title
     - Micro-frontend Customization
   * - Last Modified
     - 2021-10-29
   * - Authors
     - David Joy <djoy@edx.org>
   * - Arbiter
     - ???
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2021-10-29
   * - Review Period
     - <start - target end dates for review>
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - <links to any other relevant discussions or relevant related materials>

Abstract
========

The ability to customize the user interface of the Open edX Platform is an important requirement for all operators.  At its most basic level this means changing the logo and name of the site.  For many sites, it can also means "branding" the site so that it matches an entity's look and feel.  At its most complex, it means adding and removing functionality from the platform depending on the operator's needs.  We have historically used the word "theming" to refer to any sort of customization.

This OEP outlines the types of customization we intend to support in Open edX micro-frontends.  Micro-frontends are the technology by which we build and serve all modern Open edX user experiences.  This OEP serves as our guiding architectural strategy as we continue to develop the micro-frontend platform, and to empower implementers and maintainers as they continue to develop the platform and improve its customizability and extensibility.

Motivation
==========

An important goal of this OEP is to back away from and break down the monolithic, historical notion of "theming" into its component parts so that they can be defined and implemented individually with a common terminology.

For this reason, we try not to use the words "theming" and "branding" in this OEP, preferring "customization" as a more accurate and less loaded term.  Specific mechanisms and technologies are discussed individually so that we can be clear about their intended use cases.

Another equally important goal is to reach a common understanding of the level of official support associated with each of these component parts. Historically, we have favored customization mechanisms that are highly flexible but unmaintainable.  As our platform matures, our goal is to maintain a high level of flexibility, but via well-defined and documented extension points with clearly laid out support policies.  We also want to build in "escape hatches" that enable uncommon types of customization, even if they don't maintain the same levels of support and documentation as our other, more common use cases.

We believe that by approaching our platform's customization from this perspective, we can enable operators, developers and platform maintainers to work faster and with more confidence when working with the platform's frontend.

Specification
=============

Customization can be divided into a variety of different activities, not all of which are necessary for every Open edX instance.  These activities can be solved by a variety of mechanisms.  The spirit (and goal) of this OEP is to focus on defining categories of customization that enable the most common ~80% of customization use cases in a _supported_ and well-documented way.

This section breaks down "customization" into its component parts and discusses them individually, focusing on:

1. What the mechanism is and an overview of how it works.
2. What use cases it is appropriate for.
3. What aspects of it should be considered supported, documented, and well-maintained.

Below, we'll talk about:

1. Configuration
2. Styling
3. npm Module Overrides
4. frontend-platform Service Interfaces
5. Experience Plugins
7. Component Overrides
8. Forking

A few notable types of 'customization' that this OEP won't cover:

1. Learning content authoring
2. Copy/i18n message customization

Configuration
-------------

The first, most basic method of customization is **Configuration**.  (Note that this wouldn't be considered a part of "theming" in a historical sense, but is an important tool for "customization".)

Configuration is the most well-supported method of customization available, as it has a well-defined set of inputs and specific outputs.  Configuration is primarily useful for customization that we can anticipate and code into the platform.  It's for the most "obvious" types of customization.

We can anticipate that operators will need to change the site name, for instance, and it's just a string, so we expose it as configuration.  Feature flags are often just booleans, so we expose them as configuration as well.  This also tends to be true for any URLs to other micro-services or micro-frontends.  If the domain or path of a service/frontend can be modified, then a micro-frontends URLs to it should be as well.

Implementation Details
**********************

Micro-frontends (in the general sense) support build-time configuration in the Open edX platform.  This doesn't preclude individual micro-frontends from getting additional configuration from APIs, of course, but this OEP isn't concerned with what specific micro-frontends may do, but with our supported mechanisms across the entire platform.

Micro-frontends are configured via command line environment variables or a .env file of string key-value pairs.

As of this writing, we also plan to eventually support JSON or JavaScript-based configuration files, which will allow us to have much more flexibility in the types of configuration passed into applications, including arrays, objects, classes, etc.

Use Cases
*********

Configuration is appropriate for customizing data values that are passed to the application's JavaScript.  The most common types of configuration data are strings (like URLs) and boolean values (like feature flags) that change how the JavaScript behaves.  Configuration is generally environment-specific (production, staging, development, testing, etc.) and is about setting values used by the core functionality of the application.  Some experimental features may be enabled/disabled by configuration, or core features that are commonly turned off by some operators.

Support
*******

All configuration variables should be documented with descriptions of what they configure, example values, and indication of whether they're required.

Adding, removing, or modifying a configuration value should be documented in release notes.

Styling
-------

Styling is a very common - and thus important - method of customization.  It is frequently used to change colors, fonts, background images, and less often to modify spacing or layout.  For the purposes of this OEP, "styling" refers specifically to overriding a micro-frontend's stylesheet.

Implementation Details
**********************

Open edX micro-frontend stylesheets are generated from a `SASS <https://sass-lang.com/>`_ stylesheet at build-time, and take advantage of the Paragon design system and our "brand" packages to allow operators to substitute alternate styling during the build process, altering the final CSS.

Paragon's styles are based on Bootstrap and make heavy use of SASS variables to define colors, fonts, and spacing.  The stylesheet in a micro-frontend is composed in such a way that when the brand package is included, it acts as an override of any of the defaults specified in Paragon.  We then use an npm module override (see below) to substitute the operator's brand in place of the empty placeholder brand.

Use Cases
*********

As noted above, styling is the appropriate method of customization to change the visual look and feel of a micro-frontend, up to a point.

Styling and a brand package are also an appropriate way to override images included in the application through the stylesheet, such as images used for ``background-image`` properties.

Support
*******

We expect that all the CSS utility classes applied to our micro-frontends will be reasonably styleable without creating confusion.  For instance, we should avoid using utility classes like ``bg-white``, which implies a background should be white.  If an operator wants to change a background set to ``bg-white`` to some other color, they're creating ambiguity and confusion in their brand by having to redefine such classes.

Styling is not an appropriate way to "disable" functionality by hiding it, and the platform can't guarantee that the rest of the micro-frontend's layout will react well to a "hole" appearing in the page.

We avoid using semantic CSS classes where possible, in favor of utility classes.  We can't guarantee that semantic CSS classes applied to elements will always be present.

One thing worth noting about the styling customization process is that it must happen at build-time, since we don't ship any SASS to the client, nor is there any run-time SASS compilation in use.  This means that operators who want to run multiple brands on a single instance of the platform must independently deploy the micro-frontend multiple times, once with each brand.

npm Module Overrides
--------------------

Module overrides are a method used to replace headers, footers, and the brand package. It relies on npm's flexible module resolution syntax to resolve a module name to a _different_ module.  For example, we can update our package.json file to point ``@edx/frontend-component-header`` at a different published package, a URL, a git URL, or even a local path.  See `npm's documentation on dependencies <https://docs.npmjs.com/cli/v7/configuring-npm/package-json#dependencies>`_ for detailed usage information.

So long as the exports of the override package match those of the default, the application build process is unaware that anything has been changed.

Use Cases
*********

As mentioned above, this is in use today for three things: application headers, footers, and the "brand" package.  The mechanism could conceivably be used for any dependency listed in a micro-frontend's package.json file.

Support
*******

Practically speaking, we can only guarantee a stable set of exports for dependencies with fairly simple exports.  For the foreseeable future, this will include headers, footers, and the brand, as their supported exports are limited and stable.  Libraries like `Paragon <https://github.com/edx/paragon>`_ - which change frequently - are poor candidates for module overrides.

Support for module overrides consists of a documented set of exports.  These exports should follow the deprecation process defined in `OEP-21: Deprecation and Removal <../processes/oep-0021-proc-deprecation.rst>`_ like any other supported API.

frontend-platform Service Interfaces
------------------------------------

The frontend-platform application framework exposes a number of foundational "service interfaces" that we believe all micro-frontends should have.  The implementation used for any given service interface, such as analytics or logging, can be configured at application initialization.  This means that a micro-frontend may opt to use a different logging service instead of the default New Relic-based implementation, for instance.

Use Cases
*********

Note that there are two broad classifications of service interfaces: vendor-specific and library-specific.  In general, our vendor-specific interfaces (analytics and logging) are fully configurable and can be backed with any other implementation that fulfills the interface.

Our library-specific service interfaces, on the other hand, are much more difficult to abstract given their broader API surface.  While our auth service interface is abstracted and configurable, it would be difficult to find another HTTP client library that has a similar enough API to axios to reasonably fulfill our very axios-like interface.

Internationalization via react-intl has yet to be abstracted, and it would be exceedingly difficult to do so, as our API relies heavily on react-intl's particular implementation.

Support
*******

A service implementation that satisfies the interface should function properly, and the interfaces represent our supported API contract and should be well-documented and are subject to our deprecation workflow.

Please see the `frontend-platform documentation site <https://edx.github.io/frontend-platform/>`_ for details on the various service interfaces.

Experience Plugins
------------------

Experience plugins are our primary method of extensibility for micro-frontends.  They're the subject of their own OEP, but overlap significantly with our micro-frontend customization story.  For that reason, we'll give an overview of the parts related to customization here.

For more details, see OEP-XXXX: Micro-frontend Plugins.

Implementation Details
**********************

Experience plugins are frontend-based extensions that are embedded into the user interface of our micro-frontends at pre-defined locations called "slots".  A slot is responsible for describing - usually via CSS - the layout of any plugins it loads.  A particular slot may choose to display plugins vertically as a stacked column, or maybe as a grid of cards or a set of tabs - it's up to the slot.

Plugin slots are configured via a special "plugins" Configuration schema passed to the application like any other configuration.  The configuration includes what plugins to load and via what mechanism, their ordering, any extra configuration data that should be passed to them, and whether or not any default interface elements should be shown.

Plugins can be loaded via three primary mechanisms: module federation, iframes, and LTI (the Learning Tools Interoperability standard)

Use Cases
*********

There are a wide variety of applications for Experience Plugins.  For the purposes of customization, we're primarily concerned with those that allow us to replace interface elements/components at pre-defined extension points (slots), or extend our user experience with additional functionality.  Experience plugins are about

Support
*******

Component Overrides
-------------------

Component overrides are similar to module overrides, but for a single file.  A component override gives a developer ways of overriding specific portions of the application at a granular level.

Note that component overrides is akin to "comprehensive theming" supported in edx-platform's legacy frontend interfaces.  Ideally it shouldn't be used at all, but exists as an "escape hatch" for when all else fails.

Use Cases
*********

Component overrides should only be employed when all other methods of customization prove inappropriate or insufficient.  It's an option for operators who need to heavily customize the behavior of a micro-frontend in an uncommon way.  With many customization methods, it's expected that extension points can be upstreamed back into the platform assuming they are likely to be useful for others.  Component overrides are for those times this isn't true.

Support
*******

The _mechanism_ by which component overrides are accomplished is supported.  Specific overrides, on the other hand, are not.

Using a component override is a last resort.  In general, it involves creating a customized version of some component/file in the application that has the same exports and public functions/methods as the original file, but implements the internals in some other way.  It's important to understand that these exports and functions do not constitute a public, supported API that the platform intends to maintain.  Therefore, they may change at any time without warning or documentation.

Forking
-------

Forking is, in a sense, very similar to component overrides, but allows an override (through code modification!) of any code in the application.  Maintaining a fork of some portion of the Open edX platform is an exactly what we're trying to avoid through all these customization methods.  It is expensive and difficult to maintain, and likely to result in regressions and significant toil.

Use Cases
*********

Forking is only appropriate if an operator is intending to maintain a significantly different, derivative version of the upstream repository.  The more extensive the modifications, the more likely that it will become infeasible to stay up to date with the upstream repository.

Support
*******

Subject to the terms of the licensing agreements, forks maintained for customizations are working firmly in an "unsupported" way that the platform maintainers cannot possibly take into account when iterating.  A customization on a fork that breaks when an upstream change is merged into the repository is the responsibility of the fork maintainers to absorb.  Such changes may have varying degrees of documentation depending on whether they were made to a supported API or to the internal workings of some module, but in any case, this documentation has no bearing on whether or not any downstream forks have edited the code in question.

Rationale
=========

Regarding Comprehensive Theming
-------------------------------

Historically we've handled frontend customization through a mechanism in edx-platform called "comprehensive theming".  In short, this involves creating a parallel directory structure (which we call a "theme") which is then overlayed on top of the files in edx-platform.  If a file is present in the theme, that version is loaded in place of the original in edx-platform.

To be clear, this is a very powerful mechanism, and allows for a wide variety of customizations.  That said, while we can support the mechanism itself, it's completely unreasonable to support the "extension points" in use by those doing the customization, since they can be _anything_.  From the customizer's standpoint, it's also very difficult to maintain their theme.  It's a sparse _copy_ of edx-platform and doesn't even allow us to easily use git to manage rebasing, commits, and diffs.

Providing documentation and support for customizations via comprehensive theming is almost impossible, but if we imagine what it would take, it would involve documenting changes and/or maintaining _every single internal API in the codebase_.  This is nearly a non-sensical statement, but it illustrates the difficulty with a mechanism like this. Platform maintainers cannot possibly work on the platform without continually breaking themes at all times.

An alternate approach
---------------------

So, given the lessons learned from comprehensive theming, where do we go?  The approach outlined in this OEP is to find customization mechanisms that have both supportable mechanisms, but more importantly, supportable _contracts_.  Put another way, we should be able to document and support the technology that enables a customization mechanism, but also the way that mechanism acts and what it guarantees.

Reference Implementation
========================

This section should talk about the existing implementations for each of the customization methods listed above and also point out where they are aspirational or otherwise unfinished.

Rejected Alternatives
=====================

Discuss older OEP proposals that were rejected/never acted upon.

https://github.com/edx/open-edx-proposals/pull/79 - OEP 0028: Content theming in React
https://github.com/edx/open-edx-proposals/pull/80 - OEP 0029: Real Time Theming

Change History
==============

A list of dated sections that describes a brief summary of each revision of the
OEP.
