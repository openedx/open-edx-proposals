==================================
OEP-65: Composable Micro-frontends
==================================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-65 <oep-0065-composable-micro-frontends>`
   * - Title
     - Composable Micro-frontends
   * - Last Modified
     - 2023-12-05
   * - Authors
     - 
       * Adolfo R. Brandes <adolfo@axim.org>
       * Pedro Martello <pedro@hammerlabs.net>
   * - Arbiter
     - Adam Stankiewicz <astankiewicz@2u.com>
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2022-11-09
   * - Review Period
     - 2023-12-05 - 2023-12-19
   * - Resolution
     - TBD
   * - References
     - TBD


***********
Terminology
***********

* *MFE*: a micro-frontend
* *SPA*: single-page application


********
Abstract
********

The decision to gradually implement a micro-frontend architecture for Open edX was a largely successful attempt at
avoiding some limitations of the monolithic ``edx-platform`` codebase.  In particular, it allowed the project to
integrate more and better features by leveraging newer frontend technology, all much faster than would've been possible
otherwise.  At the same time, because features encapsulated in MFEs are very loosely coupled to each other, this
architecture eliminates most integration bottlenecks, allowing individual teams working on any given feature to be a lot
more productive.

However, roughly 3 years into this gradual re-platforming, several drawbacks to this choice of complete MFE independence
have been identified.  This OEP proposes a middle-ground based on current industry best-practices, where a minor
increase in integration costs is traded for large gains in user experience, customizability, ease of deployment, and
development efficiency.


**********
Motivation
**********

When moving to the micro-frontend architecture, a choice was made to have *completely* independent MFEs.  Each a
separate SPA, one could go as far as employing a totally different technology stack for each one.  While the goal of making
development teams be more independent, and thus at least theoretically more productive, was achieved, the price to pay
was not insignificant.  The following are examples of the impact this has had on the different classes of users of the
platform:

  * Learners: each MFE uses whatever paradigm (from design language to version of Paragon) was in vogue during its
    development, leading to noticeable differences in UX and performance; this is made progressively worse if the MFE is
    not actively maintained

  * Operators: as any given MFE can implement common features differently, or since MFEs can lag behind others in
    adoption of new versions of Paragon, more customization work is necessary to guarantee a unified experience

  * Maintainers: the job of keeping all repositories up to date is multiplied by the number of MFEs and their individual
    dependency graphs; while this comes with the territory of maintaining a large project, the fact security fixes to
    common libraries such as React or Paragon have to be considered individually per repository, for example, makes the
    job that much harder

  * Developers: Jumping from one MFE to another is now a tricky proposition; one has to challenge all assumptions: what
    is the version of React being used?  Of Paragon?  Does this MFE use the header component or not?  This makes Open
    edX frontend development, particularly taken as whole, much more difficult, negating some of the benefits of using a
    common technology stack in the first place

That's not all, though.  Letting the browser load a slightly similar but ultimately different set of packages for every
page begs for optimization.  Why make the user download 10 different versions of `react-dom` if this can be avoided?
Can we go further and avoid most page-loads in the first place?

Surely these are issues that the industry has already faced, and possibly solved.  It is thus a good time to pick
between existing solutions - specified below - weighing both the development cost of implementation and future recurring
ones against their benefits.


*************
Specification
*************

We propose a compromise between the monolithic codebase and full MFE independence: the Open edX frontend will be unified
into just a couple of single-page applications - initially just the LMS and CMS - each of which will be implemented via
a so-called "shell application".  The shell application, in turn, will render multiple MFEs as sub-components of itself,
routing between them dynamically and loading common dependencies only once.  This will require MFEs to be re-factored to
conform to the shell application's interfaces, including, for instance, the list of dependencies that they will now
inherit.

The chosen technology is `Piral <https://github.com/smapiot/piral>`_, and a reference implementation can be found in
the `frontend-app-shell <https://github.com/openedx/frontend-app-shell>`_ repository.  For the purposes of this OEP, the
latter should be taken as the canonical specification for how Piral should be used with Open edX, but the following
guiding principles should hold true:

  * MFEs should not reimplement common features such as headers, footers, and navigation, and should instead rely on the
    shell to provide them

  * Libraries elected to be common to all MFEs such as React and Paragon should be declared as peer dependencies in the
    MFE, and provided by the app shell

  * MFEs should be able to provide "hints" so that external page elements can configure themselves in context (for
    example, adding links to a header dropdown), but the MFEs need not be aware of how those hints are implemented


***********************
Implementation Strategy
***********************

It is suggested that the conversion be undertaken gradually, where each release sees a few more MFEs being pulled into the shell
application.


*********************
Rejected alternatives
*********************

At time of writing these were commonly recommended strategies for integration of MFEs into single-page apps:

* `Run-time integration via JavaScript <https://martinfowler.com/articles/micro-frontends.html#Run-timeIntegrationViaJavascript>`_
* `Run-time integration via Web Components <https://martinfowler.com/articles/micro-frontends.html#Run-timeIntegrationViaWebComponents>`_
* `Webpack Module Federation <https://webpack.js.org/concepts/module-federation/>`_
* `single-spa <https://single-spa.js.org/>`_

They were rejected in favor of Piral as per the following checklist:

  * Cost of conversion: how easy is it to convert existing MFEs to the new paradigm?
  * Cost of integration: compare the level of MFE independence.
  * Hosting of build artifacts: is it possible to host build artifacts separately on object storage or CDN?
  * Lazy loading: is it possible to load MFEs and other components lazily?
  * Efficiency of development: consider the level of boiler-plate code.
  * User impact: compare the benefits and losses from the point of view of the browser.
  * Maturity: how reliable is the codebase?  Is it maintained actively?  Can it be relied upon to exist in 5 years?
  * Project health: is the project active (measure commit activity)?  How active is the community?


**************
Change History
**************

2023-12-05
==========

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
