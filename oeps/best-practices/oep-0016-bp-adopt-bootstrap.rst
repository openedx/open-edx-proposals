##########################
OEP-16: Bootstrap Adoption
##########################

+---------------+---------------------------------------------------+
| OEP           | :doc:`OEP-16 <oep-0016-bp-adopt-bootstrap>`       |
+---------------+---------------------------------------------------+
| Title         | Bootstrap Adoption                                |
+---------------+---------------------------------------------------+
| Last Modified | 2017-06-19                                        |
+---------------+---------------------------------------------------+
| Author        | Andy Armstrong <andya@edx.org>                    |
+---------------+---------------------------------------------------+
| Arbiter       | Dennis Jen <djen@edx.org>                         |
+---------------+---------------------------------------------------+
| Status        | Accepted                                          |
+---------------+---------------------------------------------------+
| Type          | Best Practice                                     |
+---------------+---------------------------------------------------+
| Created       | 2017-04-18                                        |
+---------------+---------------------------------------------------+
| Resolution    | `Original pull request`_                          |
+---------------+---------------------------------------------------+

.. _Original pull request: https://github.com/edx/open-edx-proposals/pull/46

Abstract
########

In order to accelerate development, and provide a more consistent user
experience for our users, Open edX will adopt `Bootstrap 4`_ to style its web
applications. The community will be able to extend the platform using any of
Bootstrap's collection of components, as well as use Bootstrap theming to brand
their instances. An Open edX-supported subset of components will be provided
that are verified to support `WCAG 2.0`_ to ensure that all new user interfaces
conform to the `edX accessibility guidelines`_.

Technology Selection
####################

The following rubric was used to assess potential technologies:

* Learning curve / prior experience
* Options for community
* Frameworks that are opinionated vs. flexible
* Ability to easily build accessible front-ends
* Ease of integration
* DIY framework vs. all-inclusive platform
* Willingness to dedicate resources to development and maintenance
* Ease of prototyping
* Community project support
* Compliance and security considerations
* Availability of developers (hiring ability)
* Ability to achieve business strategy

In addition, there were a number of key goals that such a framework must
address:

* accessibility
* internationalization and right-to-left languages
* theming
* performance
* responsive designs
* extensibility / ease of overriding
* modularity / scoping
* Open edX community interest
* size and activity of the framework's community
* integration with build systems
* integration into existing edX pages

Bootstrap is the leading framework for designing web applications. It comes with
a comprehensive collection of components that are designed to be responsive,
meaning that Bootstrap applications look good at phone, tablet, and desktop
sizes.

Bootstrap 4 is the first version to support key requirements for adoption
within the edX platform:

* Bootstrap 4 has switched to using Sass (previous versions used Less)
* It provides a more flexible grid system that supports all of edX's layouts
* It uses ems and rems for responsive typography

Another key factor in the switch is the availability of Bootstrap-based
React components. With edX's move to adopt React (see
`OEP-11 - Front End Technology Standards`_), this will provide the community
with an ever increasing set of components with which to extend the platform.

Finally, a critical benefit to using Bootstrap is that the edX community
does not need to build or work with proprietary HTML classes. XBlock authors
and platform developers alike can target the standard Bootstrap classes which
are very well documented. This saves time in development, and also ensures that
the new user interface will be rendered according to the current theme.

Specification
#############

The recommendation is to adopt Bootstrap 4 starting immediately so that new
features can be built following this best practice. A new edx-bootstrap library
will be provided for installation via npm into any Open edX application. The
library will include implementations of Bootstrap themes for Open edX and
edx.org. Bootstrap themes are implemented as Sass partial files that define
values for all the customizable variables such as fonts, layouts, colors,
margins etc.

The current recommendation is that the library provide the following partials:

* A partial defining the standard edX fonts, layouts, margins etc
* An edx.org brand partial which defines the edX colors and background images
* An Open edX partial that defines the community colors and images

An Open edX site can then provide its own brand colors and images while still
importing the standard fonts, layouts, margins etc. If desired, even those
rules can be overridden to provide full flexibility in theming.

Important Considerations
########################

Bootstrap 4 Alpha Status
************************

As of May 2017, Bootstrap 4 is still only available as an alpha release. Based
upon the proof-of-concept, this does not seem to be an issue as the
functionality is very complete, and the beta release is imminent. As this is an
open source project, it is straightforward to track the work, and to provide
fixes as necessary.

Right-to-Left Language Support
******************************

Bootstrap 4 does not currently provide support for right-to-left languages,
which is clearly a roadblock for Open edX to adopt it. The official stance
from Bootstrap is that this support will be added in a minor release after
Bootstrap 4.0 is released. Given this, a solution needs to be found if
Open edX is to adopt Bootstrap earlier.

There were two primary options considered:

* Use `bi-app-sass`_ as this is the current technology used by Open edX. This
  approach generates two CSS files, one each for LTR and RTL.
* Use `rtlcss`_ which is a CSS postprocessor plugin for `postcss`_. This
  approach dynamically adds RTL-specific rules into the CSS file so that it
  can handle both directions.

The `Open edX Front End Working Group`_ discussed both options and decided that
there are several benefits of `rtlcss` over bi-app-sass:

* The default is for right/left properties to be switched which is usually
  the desired result. With bi-app-sass, every property that needs to switch
  needs to use a function, and it is very easy to forget to do so.
* For special case situations, rtlcss provides declarative comments that give
  the developer to control the behavior.
* Having only a single CSS file that adapts to the desired language
  direction makes asset management simpler.

Given this decision, the reference implementation (see below) was updated to use
`rtlcss`. This proof-of-concept successfully demonstrated pages rendered
right-to-left which confirmed the choice.

Note: once Bootstrap chooses its own approach, it will be necessary to revisit
this decision. It might be simpler, for example, to switch to use the same
technology for simplicity's sake.

For more background, see the `Bootstrap RTL Discovery ticket`_.

Theming and Multi-Tenancy
*************************

A key question for Bootstrap adoption is how to implement theming for an
application that supports multi-tenancy. Full theming encompasses many
dimensions such as CSS, images, templates, and even feature configuration. For
the purposes of an Open edX Bootstrap implementation, a solution needs to
compile new versions of each application CSS file that includes the tenant's
Bootstrap theme partial file. The application must then return the tenant's
version of each CSS file when rendered.

The technical approach for multi-tenancy is out-of-scope of this OEP, but
it is important that the edx-bootstrap library take this requirement into
account.

Backward Compatibility
######################

For edx-platform, the approach taken in the reference implementation was to
introduce a new v3 style, thus leaving all v1 and v2 styles unaffected. This
allows pages to be converted one at a time.

An investigation was performed to see whether Bootstrap components could be used
on non-Bootstrap pages, but the conclusion was that the conflicts made this more
trouble than it was worth. The recommendation is to convert pages in one shot
which will be quicker and will have fewer issues. For shared templates such as
the header or footer, a context parameter will be passed through indicating
whether Bootstrap components should be used.

Reference Implementation
########################

In order to prove the viability of Bootstrap 4, a proof-of-concept was built
that converted three different pages from the LMS:

* The course bookmarks page which was a simple first page to target
* The main courseware page which contains a number of custom styling rules
* The new course outline page which is a typical `edX Pattern Library`_ page

The following work was done:

* A new lms-main-v3.scss file was introduced that used Bootstrap
* An edX Bootstrap theme was implemented and included as a partial
* The three pages were switched over to use the v3 styling
* A "uses_bootstrap" context parameter enables a template to add Bootstrap JS
* The header was reimagined with Bootstrap components
* The page styles were updated to use Bootstrap mixins and theme variables
* RTL support was provided using `postcss`_ and the `rtlcss`_ plugin.

For the courseware, a new lms-course-v3.scss was added

* This file included all of the course-specific Sass partials
* All global-level rules were excluded to allow Bootstrap's styles to be used

See the `Bootstrap Proof-of-Concept PR`_ for more details, including screenshots
of the three converted pages. Note: the proof-of-concept was mostly completed
during a two-day hackathon, thus demonstrating how quickly these conversions can
be done.

Rejected Alternatives
#####################

edX Pattern Library
*******************

The `edX Pattern Library`_ is an initiative that was started in 2015 to
modernize the way that edX applications were built. At the time, Bootstrap was
considered to not be suitable for edX because applications always looked like
they were built with Bootstrap. In addition, Bootstrap's rules were defined
using Less and so were not compatible with edX's Sass-based styles. Finally, all
sizing was done using pixels which was not compatible with responsive
typography.

The pattern library project was somewhat successful, and a number of edX
pages were built using it. However, it became clear that there were not enough
resources available to build a comprehensive solution, and the non-standard
nature of the project meant that the community chose not to adopt it. It was
at this point that the `edX Front End Working Group`_ decided to investigate
other alternatives.

For more information, see the wiki page `State of the Pattern Library`_.

Material Design
***************

Google's Material Design is another very successful web framework that was
evaluated. It was ultimately considered to be too opinionated to support the
variety of sites in the Open edX community.

.. _bi-app-sass: https://anasnakawa.github.io/bi-app-sass/
.. _Bootstrap: http://getbootstrap.com/
.. _Bootstrap Proof-of-Concept PR: https://github.com/edx/edx-platform/pull/14834
.. _Bootstrap RTL Discovery ticket: https://openedx.atlassian.net/browse/FEDX-352
.. _Bootstrap 4: https://v4-alpha.getbootstrap.com/
.. _edX accessibility guidelines: https://edx.readthedocs.io/projects/edx-developer-guide/en/latest/conventions/accessibility.html
.. _edX Front End Working Group: https://openedx.atlassian.net/wiki/display/FEDX/Front+End+Working+Group
.. _edX UI Toolkit: http://ui-toolkit.edx.org/
.. _edX Pattern Library: https://github.com/edx/ux-pattern-library
.. _OEP-11 - Front End Technology Standards: https://open-edx-proposals.readthedocs.io/en/latest/oep-0011.html
.. _Open edX Front End Working Group: https://openedx.atlassian.net/wiki/display/FEDX/Front+End+Working+Group
.. _postcss: http://postcss.org/
.. _rtlcss: http://rtlcss.com/
.. _State of the Pattern Library: https://openedx.atlassian.net/wiki/display/FEDX/State+of+the+Pattern+Library
.. _WCAG 2.0: http://www.w3.org/TR/WCAG20/
