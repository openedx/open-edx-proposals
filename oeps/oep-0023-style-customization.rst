=============================
OEP-0023: Style Customization
=============================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-0023 </oeps/oep-0023-style-customization>`   |
+-----------------+--------------------------------------------------------+
| Title           | Style Customization                                    |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2018-06-12                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Ari Rizzitano <arizzitano@edx.org>                     |
+-----------------+--------------------------------------------------------+
| Arbiter         | Bill Filler <bfiller@edx.org>                          |
+-----------------+--------------------------------------------------------+
| Status          | Provisional                                            |
+-----------------+--------------------------------------------------------+
| Type            | Architecture                                           |
+-----------------+--------------------------------------------------------+
| Created         | 2018-06-12                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | 2018-08-03                                             |
+-----------------+--------------------------------------------------------+

Context
=======

The Open edX community relies on the capability to customize the Open
edX platform’s user interface. Providers, maintainers, and select
members of the edX core contributor team all customize the appearance of
Open edX instances to meet customer needs.

Over the Open edX platform’s lifetime, core contributors and community
members have designed and built several systems to enable interface
customization. Microsites theming [1], Stanford theming [2], and
comprehensive theming [3][4] allow maintainers to modify both
look-and-feel and interface content by overriding source files. The edX
Pattern Library [5] strove to simplify the theming process by enabling
stylistic overrides in a single place with mixed results [6]. Most
recently, an official move to Bootstrap styling has promised [7] theming
simplification, but with low adoption, the initiative has not moved
forward as quickly as hoped. The platform contains partial support for
all these theming systems, but none is guaranteed to work consistently
across the entire user interface.

None of the Open edX platform’s theming systems are officially supported
or versioned. Existing theming systems attempt to cover two broad and
orthogonal use cases: customizing look-and-feel (“style customization”)
and modifying, adding, or removing interface elements themselves
(“content overrides”). While community members rely heavily on style
customization, most of the core team (with the notable exception of the
White Label team) does not. Domain knowledge is sparse and siloed and
documentation is poorly maintained.

Without an officially supported API, core contributors are largely
unaware of what they can and cannot change. Fear of breaking existing
theming systems paralyzes the core team’s ability to make crucial
evolutionary changes to the platform’s frontend. Because existing
theming systems rely so heavily on legacy Python template rendering, the
platform frontend has been unable to evolve towards a modern,
developer-friendly, client-rendered architecture, frustrating core
contributors and community members alike.

Ironically, this same lack of awareness means undocumented breaking
changes to theming source files still occur with every release. Without
an official API, the Open edX community has no easy way to keep up with
breaking changes to templates and source files, leading to widespread
bugs and frustration. Many community members have abandoned Open edX’s
frontend entirely in favor of building their own, indicating an absolute
failure of existing theming systems for their use cases.

Decision
========

To guarantee continued core contributor support and full transparency to
the open source community, the Open edX platform must introduce official
support for both style customization and content overrides and deprecate
legacy, poorly-supported systems.

Given that style customization and content overrides address separate
end-user concerns and separate areas of the codebase (SASS vs. JS/HTML),
they will henceforth be handled by separate systems and separate APIs.
This OEP outlines proposed support for a style customization system.
Content overrides will be supported as well, but will be addressed in a
separate OEP. No existing theming systems will be deprecated until equivalent
support exists for both style customization and content overrides.

Official support for style customization requires a more limited scope than
has been allowed by prior theming systems. However, isolating customization
mechanisms to a single system reduces cognitive load for both platform
contributors and maintainers, facilitates communication of breaking
changes, and enables bolder architectural innovation.

Goals
-----

1. The Open edX core team will officially support a style customization system that
      enables contributors to easily customize look-and-feel and
      branding elements across user-facing components of all Open edX
      applications.

2. This style customization system will surface an API defining which
      elements can be modified and how. As outlined in OEP-16 [7], this
      API will be based on the Bootstrap framework.

3. As consumers of the new style customization API, the core contributor team will
      make every reasonable effort to keep breaking changes to an
      absolute minimum. If breaking changes must occur, the core
      contributor team will enumerate them with every named release of
      the Open edX platform.

4. The core contributor team will provide official documentation,
      examples, and tools to help other core contributors and open
      source community members develop themes.

5. Content overrides will not be supported within the scope of the style
      customization system, but will be supported in its own separate
      system. This system will be defined in its own separate follow-on OEP.

Plan
----

1. API definition for the new style customization system will be proposed,
      agreed upon, and documented.

2. The core contributor team will develop an example theme compliant
      with the new system and use it to build support into the platform.
      Existing work [8] on such a theme already exists in the
      edx-bootstrap repository.

3. Open edX core and community contributors will build scaffolding to
      support the new style customization system throughout the Open edX platform.
      This scaffolding will be implemented in as unobtrusive a means as
      possible with all attempts made not to interfere with existing
      theming systems.

4. Once scaffolding is complete, the core contributor team will announce
      a beta release of the new style customization system with the closest named
      release of the Open edX platform.

5. Throughout the lifecycle of the aforementioned release, the core
      contributor team will solicit feedback and contributions and
      respond to suggestions from community members willing to try out
      the new style customization system.

6. By the next sequential named release of the Open edX platform,
      assuming the new style customization system has delivered value to its beta
      stakeholders, the core contributor team will announce an official
      release of the new style customization system.

7. Removal of legacy theming-related code will begin once all its functionality
      is officially supported via the style customization and content override
      systems. If style customization is completed before content overrides, legacy
      theming code related to style customization may be removed first.


Consequences
============

-  All existing theming systems will be deprecated and support will
      eventually be removed from the platform.

-  Style customization scope will be restricted to look-and-feel and branding
      element modification. Content overrides must take place outside
      the scope of style customization.

-  The Open edX platform will gain further ability to evolve its
      frontend architecture.

-  The Open edX platform’s static asset size and build times will be
      greatly reduced.

-  Open edX core contributors must track breaking changes to the style
      customization API with every named release.

-  Documentation for the style customization system must be written and maintained.

-  Open edX community members will benefit from an officially supported,
      well understood style customization system.

-  Open edX core contributors responsible for implementing the API and
      scaffolding for the new style customization system must complete this work
      within timelines based on named release cycles.

References
==========

1. Microsites Theming
      https://github.com/edx/edx-platform/wiki/Microsites-Theming

2. Stanford Theming
      https://github.com/edx/edx-platform/wiki/Stanford-Theming

3. Comprehensive Theming
      https://github.com/edx/edx-platform/blob/master/themes/README.rst

4. Comprehensive Theming
      http://edx.readthedocs.io/projects/edx-installing-configuring-and-running/en/latest/configuration/changing_appearance/theming/index.html

5. edX Pattern Library https://github.com/edx/ux-pattern-library

6. Challenges with comprehensive theming in Eucalyptus
      https://openedx.atlassian.net/wiki/spaces/FEDX/pages/112001431/Challenges+with+comprehensive+theming+in+Eucalyptus

7. OEP-16: Bootstrap Adoption
      https://github.com/edx/open-edx-proposals/blob/a0c284c6c5f0f9419df1f5d6730b2a0c8ff26efc/oeps/oep-0016-bp-adopt-bootstrap.rst

8. edx-bootstrap edx theme
      https://github.com/edx/edx-bootstrap/tree/master/sass/edx
