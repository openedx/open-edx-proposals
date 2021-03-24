===================================
OEP-0050: Hooks extension framework
===================================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0050 </oeps/oep-0050-arch-hook-based-extensions>`
   * - Title
     - Open edX Hooks Extension Framework
   * - Last Modified
     - 2021-02-04
   * - Authors
     - Felipe Montoya <felipe.montoya@edunext.co>
   * - Arbiter
     - < TBD >
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2021-02-04
   * - Review Period
     - < TBD >

Abstract
========

To sustain the growth of the Open edX ecosystem, the business rules of the
platform must be open for extension following the open-closed principle. This
OEP specifies a solution using what is commonly referred to as hooking. That is
the definition of a stable set of places in the code (triggers) where functions
defined via plugins can take place.


Motivation
==========

Following the open-closed principle, it would be desirable for the open edX
project to allow the extension points (great `summary of the current options`_)
to modify as much of the code paths that define the business rules of an
open edX instance in a way that is consistent and repeatable.

Since the creation of the plugin framework different initiatives in the
community have demonstrated success by adopting this pattern. This OEP extends
the range of possible extensions that the patter can hold sustainably.

Adopters of the Open edX technology as a way of delivering online courses very
often require that certain changes are made to the codebase by changing the
edx-platform core. This proposal aims to be a solution to organize the majority
of the changes that the authors have encountered after years of work extending
the core in a way that is more compatible with the community at large, it is
easier to reuse and far easier to migrate to newer versions of the codebase.

This proposal draws inspiration from the cumulative experience of eduNEXT as the
lead author, other members of the community, and from one of the largest
extendable platforms for the web today for many years already, WordPress.

.. _summary of the current options: https://github.com/edx/edx-platform/blob/master/docs/guides/extension_points.rst


Specification
=============

The core of this proposed framework is the definition of a stable list of places
in the life cycle of the learners registration, login, enrollment, course usage,
and certification. Conversely, the authors life cycle will have a list of places
during the creation and publication of courses. These places, called triggers
will be written to the edx-platform code.
Now, anyone that wants to extend the functionality of the platform at one of
those places, can define a function, called an action or filter, to be called
when the trigger is executed. The sum of a trigger and action will be referred
to as a hook.

Triggers, broadly fit into two categories:

* Triggers that allow actions called at this time to affect the application flow
  via their results. In this case, the actions called at the time will be called
  filters.
* Triggers that will not allow the application flow to be altered by the
  actions called.

Although the first document contains a list of the proposed hooks, we now
proposed that the documentation for this is kept in-code at https://github.com/edx/edx-platform/tree/master/docs/guides.
This location should contain a document that covers the design principles and
details that did not land in the OEP as well as the final decisions made in the
OEP. Also will index the list of hooks and their current status. It should not
cover the history and all the previous states of the framework. It should
reflect the latest way of thinking and examples of how to use the framework.

Additionally, a directory in the same location will include individual files
that cover each available trigger.

The signature of the functions (input/output) of every trigger should be
maintained in a stable way. The same applies for additions or removals from the
list of public triggers, which should follow a path of deprecation first and
preferably be given an optional hook where the same context information or code
execution path can be accessed.

The mechanism by which a trigger is connected to an action/filter will be a
configurable python wrapper also contained in the platform core and by design
must consider that:

* Most actions will be implemented in plugins.
* The order of execution for multiple actions/filters listening on a trigger
  must be respected, and the results of previously executed filters/actions
  should be made available to the current filter/action.
* Actions should be easy to execute in an asynchronous way.
* The results of executing actions or filters in an asynchronous way will be
  ignored by the calling process. The will not modify the flow of the application


The wrapper should present functions to register the trigger call, to call the
configured actions as a pipeline, and to defer calling the functions as async
tasks when configured. This wrapper should make the triggers, especially those
that do not allow for application flow change, to be compatible with the Async
Event Messaging described in OEP-41.
In terms of error handling, it should allow the configuration of silent failures
letting operators know the source of the error.


Rationale
=========

Hooks are a way for one piece of code to interact/modify another piece of code
at specific, pre-defined spots. Many of the business rules of an installation
can be altered and extended if the right spots are allowed to be extended by
open edX plugins. Initially, this proposal is meant as a way for community
members to extend the platform, but it could even become a way for the
installation at edx.org to implement its particular business rules in a way that
is sufficiently separated from the Open edX core.

This proposal draws heavy inspiration from WordPress, in which the same
`two types of hooks`_ are defined. Leveraging the Python/Django based technology
of open edX makes actions a great candidate to be executed asynchronously in a
different process as they are meant to be called with some context data, act on
it, and return silently to the trigger location.
Filters on the other hand are designed so that they are given some context data,
act on it, and return a modified version for the rest of the core-code to use in
the next steps of execution. We intend to make the two kinds of hooks
interchangeable such that developers are not limited by the framework but
instead given maximum flexibility on its usage to attain their extension goals.

Hooks are used within WordPress to support almost all the forms in which it can
be extended and are the basis of the very successful marketplace for plugins and
themes. Given that Open edX already has a variety of theming capabilities and
options we have opted here not to include any hooks that are meant to be used
for theming.

This proposal does not copy the model verbatim, since the open edX platform uses
a different base technology which already allows for many extension points.
This proposal deals primarily with the specific spots in the code execution that
for many initiatives have been identified as the critical points that would
allow them to adopt this technology without requiring modifications to the core.

On the technical side, Django and the support for auto-installed plugins using
stevedore already allow the addition of code in a very clean way. This proposal
builds on top of this, only trying to standardize and grow the current
capabilities of the platform.

.. _two types of hooks: https://developer.wordpress.org/plugins/hooks/#actions-vs-filters


Performance Considerations
--------------------------

This proposal is a double-edged sword. By allowing plugins to install code on so
many and so varied places of the application performance could be completely
destroyed to the point of making the platform unusable.

This is a very important topic, but we believe that any responsible
implementation of a hook must keep its own consequences and risks in check.
In other words, it is up to the users of the hooks framework to be mindful of
the performance of the platform by connecting to the appropriate triggers and
optimizing their actions to the appropriate scale their Open edX instance runs on.

Using asynchronous tasks to fulfill the actions and moreover connecting actions
to the triggers in a way that is directly asynchronous is strongly recommended.
The initial implementation must include sufficient tooling to make this possible
and encourage it.

Alternatively, in the core platform code, the actual exposition of each
particular trigger could be configured (via flags or settings) so that even for
scales at which the simple lookup for the actions that might be connected to a
particular trigger could have performance implications is accounted for.


Backward Compatibility
======================

The only extension point that will be affected by this OEP is the
REGISTRATION_EXTENSION_FORM. This extension point should still be supported
until a clear migration path to the corresponding hook is well defined and
documented.


Reference Implementation
========================

The reference implementation must be completed before this OEP is given "Final"
status. This document will be updated as the implementation progresses.


Rejected Alternatives
=====================

* Django Signals was initially considered as the primary mechanism to connect
triggers and actions, but was ultimately discarded due to the lack of control
of the execution order when more than one action is listening for a trigger.

* It was considered to split this OEP into two. One handling the triggers that
do not allow for flow modification and then a second handling those that do.
This was however rejected fearing that the proposal could be stuck only after
the first iteration and never reach its full potential. Instead a phased
implementation of triggers from both kinds will be pursued.


References
==========

This OEP was first discussed and presented to the community via `a public google doc`_.
The discussion held there is included into the present proposal.

.. _a public google doc: https://docs.google.com/document/d/1jhnudz6AVtVt0ZSRSwOwj9gJ0lsDDn_8mUCPehLPzLw/edit#


Change History
==============

28 July 2020 - Felipe Montoya.
Initial version made public for the community at the google docs file.

04 Februaty 2021 - Felipe Montoya
Converted to Draft OEP
