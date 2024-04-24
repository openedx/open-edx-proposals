OEP-68: Frontend Best Practices
###############################

.. list-table::
   :widths: 25 75

   * - OEP
     - Link to the doc in the following format::

        :doc:`OEP-XXXX <oep-XXXX-YYYY-ZZZZ>`

        * <XXXX is the next available OEP number>
        * <YYYY is the abbreviated Type: proc | bp | arch>
        * <ZZZZ is a brief (< 5 words) version of the title>

   * - Title
     - <OEP title>
   * - Last Modified
     - <date string, in YYYY-MM-DD format>
   * - Authors
     - <list of authors' real names and optionally, email addresses>
   * - Arbiter
     - <Arbiter's real name and email address>
   * - Status
     - <Draft | Under Review | Deferred | Provisional | Accepted | Rejected | Withdrawn | Final | Replaced>
   * - Type
     - <Architecture | Best Practice | Process>
   * - Created
     - <date created on, in YYYY-MM-DD format>
   * - Review Period
     - <start - target end dates for review>
   * - Resolution
     - <links to any discussions where the final status was decided>
   * - References
     - <links to any other relevant discussions or relevant related materials>

Abstract
********



Motivation
**********

In the Openedx ecosystem, we have a complex Microfrontend architecture that follows
architecture principles like modular boundaries. However, a well-working architecture
like this also needs to rely on code not breaking these principles. Modular boundaries
for example have little value if the code does not respect them; however,
some of our code routinely does, which makes this OEP important.
This OEP aims to establish some basic code quality standards that we can agree upon
to guide us. The goal of course is for our applications and ecosystem to be functional,
reliable, performant, easy to use, maintainable, secure, portable and scalable.

Specification
*************

Ultimately, the goal is for our applications and ecosystem to be functional,
reliable, performant, easy to use, maintainable, secure, portable and scalable.
This is a good benchmark for quality standards proposed: if the proposed standard
does not help us achieve these goals, it is likely not a good standard.

CSS Quality Standards
=====================

CSS is famous for being laden with conflicts between different files and statements.
All the most important modern UI and CSS frameworks attempt to deliver some solution
to this problem, and standards like BEM that only exist for this reason
have been around for a long time. Our own Paragon component library uses Bootstrap,
which attempts to keep modular. However, in the past we didn't have standards
for CSS code that was living outside of paragon. This has caused bugs and conflicts.

An important cause of problems has been CSS that is not just in conflict with other CSS
in the same Microfrontend but even with CSS in other Microfrontends, which produces
extremely unpredictable bugs. For example, if you write some CSS in the Header or Footer
components, that will override classes with the same names in Course-Authoring and won't
show up in tests.

This ADR will prescribe modular CSS but will not choose a certain technology or
pattern to implement this. There are many solutions that are good.

The following standards are proposed:
- CSS in one Microfrontend must not be able to affect HTML / components in any other
  Microfrontend.
- CSS belonging to one React component or HTML file must not be able to affect any other
  HTML or components except for children.
  This means that CSS must be scoped to the component.
- When you export a component for example from a component library, the CSS attached
  must be scoped to that component and not affect anything outside of it.
- When you import a component into your code, you should rely on the CSS it comes with,
  and then you could override that CSS with CSS in your own code that's more specific, for
  example. However, This code overriding the important component can only override it
  in the place you are currently importing it. It must not override the imported
  component's CSS in any other place that it's used.

Rationale
*********

The rationale adds to the specification by describing the events or
requirements that led to the proposal, what influenced the design, and why
particular design decisions were made. The rationale could provide evidence
of consensus within the community and discuss important objections or
concerns raised during discussion. It could identify any related work,
for example, how the feature is supported in other systems.

Backward Compatibility
**********************

This statement identifies whether the proposed change is backward compatible.
An OEP that introduces backward incompatibilities must describe the
incompatibilities, with their severity and an explanation of how you propose to
address these incompatibilities.

Reference Implementation
************************

There are many ways to implement this, and we need to make sure we don't create
conflicts between different ways to do it. That can mean that we make an openedx-wide
decision on the technology used or, if there are diverse needs,
that we use different technologies but ensure they are compatible. As stated,
this OEP is agnostic to this choice.

However, if no other solution is chosen, the following is the minimal requirement
for any new CSS in openedx:

- A CSS file cannot just define CSS for a selector like `.button`, because
  this class name may be used in other places and you may overwrite other CSS in other
  places by accident.
- CSS must always be scoped to the MFE or repository it lives in, whether that's
  an MFE, a component library, an HTML file, or anything else. This means that the
  top-level HTML element of this repository needs to get a unique class or id;
  for example the top-level element of the `frontend-component-header` repository
  may simply have a class `.frontend-component-header` since repository names are unique,
  and no other HTML element may have a class that's an openedx repository name.
  Every CSS statement in this repo needs to include this class in its selector;
  with SASS it's easy to just wrap everything in `.frontend-component-header {`.
- Every component or HTML file that has CSS needs to have a top-level html element
  with a classname that is unique within the repository (because of scoping rules above
  it should be fine if the name re-occurs in a different repository). Then all
  CSS that belongs to this component or HTML file needs to be wrapped in this top-level html
  selector.

Rejected Alternatives
*********************

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

Change History
**************

YYYY-MM-DD
==========

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
