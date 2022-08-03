OEP-6: Context-scoped XBlock Fields
###################################

+---------------+----------------------------------------------------------+
| OEP           | :doc:`OEP-6 <oep-0006-arch-context-xblock-fields>`       |
+---------------+----------------------------------------------------------+
| Title         | Context-scoped XBlock Fields                             |
+---------------+----------------------------------------------------------+
| Last Modified | 2016-12-14                                               |
+---------------+----------------------------------------------------------+
| Author        | Braden MacDonald <braden@opencraft.com>                  |
+---------------+----------------------------------------------------------+
| Arbiter       | Calen Pennington <cale@edx.org>                          |
+---------------+----------------------------------------------------------+
| Status        | Provisional                                              |
+---------------+----------------------------------------------------------+
| Type          | Architecture                                             |
+---------------+----------------------------------------------------------+
|  Created      | 2016-08-03                                               |
+---------------+----------------------------------------------------------+
| `Resolution`  |                                                          |
+---------------+----------------------------------------------------------+



Abstract
********

There is currently no standard way for XBlocks to define data fields such that
content, settings, or user input can be shared among multiple XBlocks within a
particular "context" such as a course. Such "course-scoped" or "context-scoped"
fields are a common requirement; for example, an author may wish to specify the
default "number of attempts allowed" once for all problems in a course, or a
video player XBlock may want to "remember" the user's preferred video playback
speed for videos within a particular course.

This OEP proposes a new mechanism for defining XBlock fields that are explicitly
scoped to a particular context. The generic term "context" refers to a course,
content library, blog post, or any other place where XBlocks can be used (see
"Rationale").



Background
**********

The XBlock API uses the abstract notion of "`scopes`_" to associate each field
with particular environment(s) and user(s). A field's ``Scope`` is generally a
composite property, defined as the combination of a ``BlockScope``, a
``UserScope``, and a ``name``.

.. _scopes: https://edx.readthedocs.io/projects/xblock/en/latest/fields.html

For reference, the ``UserScope`` can have three values (no changes to these are
being proposed):

* ``NONE``: data not related any particular user, that does not change while
  users interact with the XBlock
* ``ALL``: data not related to any particular user, but which may be modified
  during user interactions
* ``ONE``: data related to an individual user

The ``BlockScope`` can have the following values:

* ``USAGE``: "The data is related to a particular use of a block in a course."
* ``DEFINITION``: "The data is related to the definition of the block" (one block definition can be used multiple times in a course or even among multiple courses).
* ``TYPE``: "The data is related to all instances of this type of XBlock."
* ``ALL``: "The data is common to all blocks."

XBlock runtimes would generally not be expected to implement every possible
combination of ``BlockScope`` and ``UserScope``. Instead, certain "named scopes"
are defined, which are the only scopes that most runtimes support:

The existing named scopes, and an example use case for each, are as follows:

* ``Scope.content`` (UserScope.NONE, BlockScope.DEFINITION):

  * Example: The text of a multiple choice question
* ``Scope.settings`` (UserScope.NONE, BlockScope.USAGE)

  * Example: The number of attempts allowed for a problem
* ``Scope.user_state`` (UserScope.ONE, BlockScope.USAGE)

  * Example: The student's answer to a multiple choice question
* ``Scope.user_state_summary`` (UserScope.ALL, BlockScope.USAGE)

  * Example: The number of students who have answered the question (side note:
    there is no locking or atomicity for XBlock field writes, so in practice
    this field is currently of little use in a MOOC)
* ``Scope.user_info`` (UserScope.ONE, BlockScope.ALL)

  * Example: user age (note: this scope seems little-used and overlaps with the
    user service)
* ``Scope.preferences`` (UserScope.ONE, BlockScope.TYPE)

  * Example: (For a video XBlock) Boolean setting to prefer YouTube or
    non-YouTube video playback.



Motivation
**********

As one would expect, ``Scope.user_info`` and ``Scope.preferences`` fields in the
Open edX platform are implemented globally, and not scoped to the course that
uses them. There is currently no standard, extensible way to use XBlock fields
to store data that is scoped to a particular "context," such as a course,
although this is a common need.

In the absence of a formal method for achieving context-scoped fields through
the XBlock API, many other solutions have been put in place, such as: adding
custom Django models to XBlocks, storing fields on the root "course" XBlock,
defining inherited fields using InheritanceMixin, using edx-submissions as a
generic data store, and more.

It is hoped that the new canonical mechanism described by this OEP can provide
consistency, improve interoperability, allow new types of behavior to be
implemented by XBlocks, and give both authors and learners additional control
over learner interactions with XBlocks.



Specification
*************

New BlockScope value: CONTEXT
=============================

To support context-scoped fields, ``BlockScope`` will allow a new value:
``CONTEXT``. This new ``BlockScope`` will denote data that is related to all
instances of this type of XBlock within a particular context.

A "context" is defined by the runtime but is usually a "course" or the nearest
equivalent. In the Open edX platform, the context may be a course or a content
library. Different runs of the same course would be considered different
contexts and would not share data, though UserScope.NONE fields would be copied
when creating a new course run. On other platforms, a "context" could be defined
as a blog post or a textbook.

New named scopes
================

As is the situtation today, XBlock runtimes would generally not be expected to
implement every possible combination of ``BlockScope`` and ``UserScope``.
Instead, XBlock runtimes are encouraged to implement these new named scopes,
which would be defined in the XBlock python package:

* ``Scope.context_settings`` (UserScope.NONE, BlockScope.CONTEXT)

  * Example: For an LTI XBlock, a list of the LTI provider URLs and secrets used
    in the course
  * Example: For a discussions XBlock: Enable or disable discussions in this
    course, as well as settings like "allow anonymous discussion posts", "allow
    anonymous discussion posts to peers", "discussion blackout dates", etc.

* ``Scope.context_preferences`` (UserScope.ONE, BlockScope.CONTEXT)

  * To avoid performance issues, runtimes are not required to support field
    values larger than 4 KiB for fields that use this scope. This restriction is
    intended to avoid the temptation to use this type of XBlock field for
    logging or message passing. Such uses could cause data loss or performance
    concerns, because the XBlock API does not support appends nor locking for
    field data.
  * Example: Enable or disable notifications about new discussion posts in this
    course
  * Example: Video playback speed for this course
  * Example: An XBlock in which students "accumulate building blocks over the
    course of the semester" (`PLAT-325`_)

.. _PLAT-325: https://openedx.atlassian.net/browse/PLAT-325

New XBlock view: context_settings_view
======================================

An XBlock which uses ``Scope.context_settings`` fields will need a way for
content authors to edit the data in those fields. (e.g. when creating a course,
the course author may wish to change course-wide settings for the discussion
XBlock used throughout that course.)

A new XBlock view, ``context_settings_view`` shall be used for this purpose.
Just like the existing `studio_view` defined by edX Studio, this new view will
allow an XBlock to return an arbitrary HTML Fragment which contains a form or
other UI for editing its ``Scope.context_settings`` fields.

It is expected (but not required) that runtimes like edX Studio which want to
support editing of context-scoped fields will define a list of XBlocks that are
enabled within a particular context/course, and will display a list of XBlocks
that define the ``context_settings_view``. Clicking on the name of that XBlock
would display the HTML fragment and allow the author to edit the context-scoped
fields.

.. _studio_view: https://edx.readthedocs.io/projects/xblock-tutorial/en/latest/edx_platform/edx_studio.html

Context-scoped fields in OLX (Provisional)
==========================================

When serializing a course with ``Scope.context_settings`` fields to Open
Learning XML (OLX), any context-scoped fields could be described within a new
``<xblock-settings>`` XML element that is a child of the ``<course>`` element.
This would typically be found in ``course/course.xml``. Each XBlock class that
has ``Scope.context_settings`` field data could be represented as a child
element of ``<xblock-settings>``, with the name of the element matching the
XBlock's entry point name.

An example follows:

.. code-block:: xml

   <course display_name="Example Course" language="en" advanced_modules='["drag-and-drop-v2", "lti_consumer", "xblock-dalite"]'>
     <chapter url_name="6915ee3dd6ab403d8c05c0ea3180a0ee"/>
     <wiki slug="A.B.C"/>
     <xblock-settings>
       <discussion allow_anonymous_discussion_posts="true" discussion_blackout_dates='[["2015-09-15", "2015-09-21"], ["2015-10-01", "2015-10-08"]]' />
       <lti_consumer lti_passports='["myapp:key1:secret1", "otherapp:key2:secret2"]' />
       <xblock-dalite dalite_lti_passports='["dalite-ng-demo:https://dalite-ng-demo.example:key:secret"]' />
     </xblock-settings>
   </course>

The reasoning behind the above provisional spec is as follows:

* Conceptually, in the OLX format, all course content and all XBlock fields are
  serialized as XML nodes, within a root ``<course>`` node `(docs)`_. The entire
  course's XML may be in a single course.xml file or spread out into multiple
  files and subdirectories, where the subdirectory name is the XBlock entry
  point name (XML node name) and the file name is the block ID (``url_name``).
* Since context-scoped fields are XBlock fields just like Scope.content fields,
  they should be serialized to OLX the same way (XML, not JSON).
* Many existing course-scoped settings that are analogous to context-scoped
  fields (discussion settings, LTI passports, etc.) are currently stored as
  attributes on the ``<course>`` XML element.
* The option of serializing context-scoped fields as namespaced attributes on
  the ``<course>`` element is not feasible as it would be messy and conflicts
  with the current approach where almost every attribute is an XBlock field
  belonging to the course XBlock.
* ``<xblock-settings>`` is suggested as a wrapper to keep these new XML nodes
  organized and indicate their purpose clearly.

.. _(docs): https://edx.readthedocs.io/projects/edx-open-learning-xml/en/latest/directory-structure.html#top-level-directory

Rationale
*********

Many of the ideas in this proposal are driven by the desire for new types of
functionality to be implemented via XBlocks, and thus to be pluggable and easily
installed, enabled, or disabled for each Open edX installation or each course.

The idea of naming the new scope "context" comes from other technologies such as
LTI, where the term "context" is used analogously. For example, when embedding
an LTI tool into the Open edX LMS, the LMS will pass the current course ID as
the LTI "context" parameter.

Previous discussions of note include:

* https://github.com/edx/open-edx-proposals/pull/15
* https://openedx.atlassian.net/browse/PLAT-325
* https://groups.google.com/d/msg/edx-code/ywjXV0wzQiw/FRzaK5nTAgAJ
* On 2016-07-08, the author met with several stakeholders from edX (Calen
  Pennington, David Ormsbee, and Robert Raposa) to discuss course-scoped XBlock
  fields. The discussion tended toward using "context" as an additional
  dimension (see "Context as a New Scope Dimension" in "Rejected Alternatives"),
  but we did not think of any use cases for 8 of the 12 additional scopes that
  would be created by defining an additional dimension.

Other related proposals of note include:

* https://github.com/edx/XBlock/pull/317 - XBlock field sharing
* https://openedx.atlassian.net/wiki/display/AC/Feature+Plugins+for+edX+Platform#FeaturePluginsforedXPlatform-Plug-inArchitecture - Plug-in Architecture



Backward Compatibility
***********************

This OEP introduces new field scopes but does not remove or modify any existing
functionality. XBlock authors that add new fields to their XBlock may opt-in to
using this feature.

XBlock runtimes may choose to implement context-scoped fields or not. Attempting
to access a context-scoped field in a runtime that does not support such fields
should simply return the default value of the field. Attempting to modify such a
context-scoped field in a runtime that doesn't support such fields should raise
an ``InvalidScopeError``.

Some existing XBlocks currently use workarounds for the lack of context-scoped
fields, such as storing data in the fields of the root 'course' XBlock.
Converting such existing techniques to use context-scoped fields instead would
be best achieved with an XBlock data migration API, which currently does not
exist and is outside the scope of this OEP (however, see `here`_ for an example
implementation).

.. _here: https://github.com/open-craft/problem-builder/blob/c5879dd/problem_builder/v1/xml_changes.py

Details on the current implementation of field scopes in edx-platform can be
found in `this gist`_. The `field test XBlock`_ can be used to test how a
runtime behaves with different field scopes.

.. _this GIST: https://gist.github.com/bradenmacdonald/b576c59e46af8df9949fa84b3bc7a4b6
.. _field test XBlock: https://github.com/open-craft/xblock-field-test



Open Questions
**************

#. Should runtimes like the LMS be expected to implement a "Preferences" tab for
   each course that allows students to edit all ``Scope.context_preferences``
   fields for XBlocks used in that course?

#. If yes (see previous bullet), can we use a field attribute to allow XBlocks
   to opt-out of this auto-generated settings UIs on a per-field basis? This may
   best be addressed at the same time as a standard API for definining editor UI
   options such as ``allow_reset``, ``multiline_editor``, ``list_style``, and
   ``values_provider`` as implemented in `xblock-utils`_.

#. Do we need to consider supporting contexts smaller than a course, such as a
   section or exam? And if so, could that be done in a future change, such as
   adding a non-scope field property to select a context which defaults to
   "course"?

   * The author's thought on this is that we likely cannot come up with a
     reasonable proposal to support every possible use case, and it's better to
     offer simple, flexible, easily understandable support for the main "course"
     use case now.

#. Should there be a way to define a field such that the runtime will search
   through a hierarchy of ``BlockScope`` scopes and use the first value found,
   such as "check for a ``Scope.settings`` value, then a
   ``Scope.context_settings`` value"?

.. _xblock-utils: https://github.com/edx/xblock-utils/blob/48fa1a9/xblockutils/studio_editable.py



Reference Implementation
************************

TBD. Sample code to implement this feature in the XBlock package and the XBlock
SDK/Workbench will be produced once the draft has had some initial scrutiny and
feedback.



Rejected Alternatives
*********************

Additional Scopes
=================

This proposal originally included two new ``BlockScope`` values: ``CONTEXT``,
and ``CONTEXT_ALL_TYPES``; the latter was a proposed new scope that would be
used for field data that is accessible to XBlocks of any type, within a
particular context.

It also defined two named scopes that used ``CONTEXT_ALL_TYPES``:

* ``Scope.context_shared_preferences`` (UserScope.ONE, BlockScope.CONTEXT_ALL_TYPES)

  * This scope has been dropped from the proposal as it seems to lack a clear
    and compelling use case.

* ``Scope.context_shared_settings`` (UserScope.NONE, BlockScope.CONTEXT_ALL_TYPES)

  * This scope has been dropped from the proposal as it seems to lack a clear
    and compelling use case; further, it is unclear how an authoring experience
    for such fields would work, since no one XBlock could provide an authoring
    UI for fields that could be shared by many unrelated XBlocks.


Inheritance
===========

The current proposal deliberately does not support inheritance (i.e. a "context"
in the LMS would always be a course, not a "section" or any smaller unit, and
field values don't propagate from parent blocks to children), so it is not a
replacement for `InheritanceMixin`_.

Initial discussions of this OEP agreed that context-scoped fields should not
participate in inheritance. Improving inheritance of fields or making
inheritable fields pluggable are worthy goals, but for a separate proposal.

.. _InheritanceMixin: https://github.com/edx/edx-platform/blob/46d69eba7ac45/common/lib/xmodule/xmodule/modulestore/inheritance.py#L30

Context as a New Scope Dimension
================================

Currently, a ``BlockScope``, a ``UserScope``, and a ``name`` are combined to
make a Scope for a field. Under this alternative option, a new
``RelevanceScope`` dimension would be added, which could have one of two values:
``ALL`` (default) or ``CONTEXT``. The ``ALL`` value would represent the current
XBlock API's definitions, and the ``CONTEXT`` value would mean that the field's
relevance is limited to one particular context (e.g. that field's value relates
to one particular course.)

The downside of this is that it creates a large matrix/tensor of scopes:
4 BlockScopes × 3 UserScopes × 2 RelevanceScopes = 24 distinct scope types.
Not only is this more difficult to understand, but there is no value in defining
a "context-specific" version of any ``BlockScope.USAGE`` or
``BlockScope.DEFINITION`` fields. As a result of that and other conflicting or
not useful Scope combinations, it is likely that fewer than half of the 24
possible Scope values would be used.

Shared data service
===================

Another alternate option is to create a "shared data" or "course-scoped data"
XBlock service that XBlocks can use to read and write data at a course level,
rather than making this a new type of field. Such an approach seems less
consistent, and would not allow the automatic creation of a UI for editing
context-scoped XBlock settings. (With the field approach, we can replace the
"Advanced Settings" page in Studio with an auto-generated UI that allows authors
to configure the course-scoped settings of all XBlocks enabled for that course.
We can also generate a "preferences" tab in the LMS that allows students to
change XBlock preferences for that course.)



Copyright
*********

.. image:: https://i.creativecommons.org/l/by-sa/4.0/88x31.png
    :alt: Creative Commons License CC-BY-SA
    :target: http://creativecommons.org/licenses/by-sa/4.0/

This work is licensed under a `Creative Commons Attribution-ShareAlike 4.0
International License`_.

.. _Creative Commons Attribution-ShareAlike 4.0 International License: https://creativecommons.org/licenses/by-sa/4.0/
