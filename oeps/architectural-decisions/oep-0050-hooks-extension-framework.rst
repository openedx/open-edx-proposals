.. _OEP-50 Hooks extension framework:

OEP-50: Hooks extension framework
#################################

.. list-table::
   :widths: 25 75

   * - OEP
     - :ref:`OEP-50 Hooks extension framework`
   * - Title
     - Open edX Hooks Extension Framework
   * - Last Modified
     - 2021-04-17
   * - Authors
     - Felipe Montoya <felipe.montoya@edunext.co>
   * - Arbiter
     - Nizar Mahmoud <nizar@opencraft.com>
   * - Status
     - Accepted
   * - Type
     - Architecture
   * - Created
     - 2021-02-04
   * - Review Period
     - 2021-02-04 - 2021-04-30

Abstract
********

To sustain the growth of the Open edX ecosystem, the business rules of the
platform must be open for extension following the open-closed principle. This
OEP specifies a solution using what is commonly referred to as hooking. That is
the definition of a stable set of places in the code (hooks) where functions
defined via plugins can take place.


Motivation
**********

Following the open-closed principle, it would be desirable for the open edX
project to allow the extension points (great :doc:`summary of the current options<docs.openedx.org:developers/concepts/hooks_extension_framework>`)
to modify as much of the code paths that define the business rules of an
open edX instance in a way that is consistent and repeatable.

Since the creation of the plugin framework different initiatives in the
community have demonstrated success by adopting this pattern. This OEP extends
the range of possible extensions that the pattern can hold sustainably.

Adopters of the Open edX technology as a way of delivering online courses very
often require that certain changes are made to the codebase by changing the
edx-platform core. This proposal aims to be a solution to organize the majority
of the changes that the authors have encountered after years of work extending
the core in a way that is more compatible with the community at large, it is
easier to reuse and far easier to migrate to newer versions of the codebase.

This proposal draws inspiration from the cumulative experience of eduNEXT as the
lead author, other members of the community, and from one of the largest
extendable platforms for the web today for many years already, WordPress.

Specification
*************

The core of this proposed framework is to define a stable list of places, called
hooks from now on, in the life cycle of the learners registration, login,
enrollment, course usage, and certification. Conversely, the authors life cycle
will have a list of hooks during the creation and publication of courses.

According to the expectations of the calling location, hooks can be split into
two, namely events and filters. Events run when called at a given location and
return nothing. Filters on the other hand are passed data and are expected to
return something. This something will be used by the platform code. The most
common case is to be passed data and return the same of somewhat changed, but
similar data. Other common case would be to receive data and raise an exception
in accordance with the definition of the hook. Most likely to completely halt
the process that would happen after the hook.

The documentation for the available hooks at any point should be kept in-code at
https://github.com/openedx/edx-platform/tree/master/docs/guides. This location
should contain a document that covers the design principles and details that did
not land in the OEP as well as the final decisions made in the OEP. Also will
index the list of hooks and their current status. It should not cover the
history and all the previous states of the framework. It should reflect the
latest way of thinking and examples of how to use the framework.

Given the public promise made by the framework, the signature of the functions
(input/output) of every hook should be maintained in a stable way. The same
applies for additions or removals from the list of public triggers, which should
follow a path of deprecation first and preferably be given an optional hook
where the same context information or code execution path can be accessed.

Registering more than one function for a hook should be possible. This creates
two different interfaces given that filters are expected to return something and
the platform should act accordingly. Due to this different nature, the mechanism
by which a developer connects to a hook differs from events to filters.

Events will be called via Django signals. There will be a new library that
defines a list of semantically meaningful signals based on higher-level events
and platform concepts. This library will be imported at the platform code and
will, when possible, replace the current signals the platform already emits.
Developers that want to use a hook, should also import this library at the
plugins where they are writing the functions that connect to the event.

For filters, the possibility of registering more than one function makes some
sort of reducing routine for the results necessary. Therefore the proposal is to
make them work as a pipeline, so the results of the previously executed function
should be made available to the next function. Doing so makes the calling order
of its registered functions important and thus it must be respected. This is not
supported by Django signals and for this case, the mechanism to register
functions will be the Django settings module.

When possible, hooks should be compatible with the Async Event Messaging
described in OEP-41.
In terms of error handling, the implementation should allow the configuration of
silent failures letting operators know the source of the error.


Rationale
*********

Hooks are a way for one piece of code to interact/modify another piece of code
at specific, pre-defined spots. Many of the business rules of an installation
can be altered and extended if the right spots are allowed to be extended by
open edX plugins. Initially, this proposal is meant as a way for community
members to extend the platform, but it could even become a way for the
installation at edx.org to implement some of its particular business rules in a
way that is sufficiently separated from the Open edX core.

This proposal draws heavy inspiration from WordPress, although it does not copy
the concepts verbatim and leaves some space for leveraging the possibilities
that Python and Django have to offer. For example, events are great candidates
for asynchronous execution in a different process as they are meant to be called
with some context data, act on it, and return silently to the trigger location.
Developers are encouraged to use the pattern of deferring to async tasks
immediately using celery.

Hooks are used within WordPress to support almost all the forms in which it can
be extended and are the basis of the very successful marketplace for plugins and
themes. Given that Open edX already has a variety of theming capabilities and
options we have opted here not to include any hooks that are meant to be used
for theming.
This proposal deals primarily with the specific spots in the code execution that
for many initiatives have been identified as the critical points that would
allow them to adopt this technology without requiring modifications to the core.

On the technical side, Django and the support for auto-installed plugins using
stevedore already allow the addition of code in a very clean way. This proposal
builds on top of this, only trying to standardize and grow the current
capabilities of the platform.


Performance Considerations
==========================

This proposal is a double-edged sword. By allowing plugins to install code on so
many and so varied places of the application, performance could be hindered.

This is a very important topic, but we believe that any responsible
implementation of a hook must keep its own consequences and risks in check.
In other words, it is up to the users of the hooks framework to be mindful of
the performance of the platform by connecting to the appropriate hooks and
optimizing their functions to the appropriate scale their Open edX instance runs
at.

Receiving an event signal and immediately registering an async task using celery
is a well-known pattern through the code and strongly recommended.
Making this a core part of the configurations was removed from the proposal to
make the framework less complicated in its implementation.

Also in the core platform code, the actual exposition of each particular hook
could be configured (via flags or settings) so that even for big scales, at
which the simple lookup for the functions or sending of the signals for a hook
could have performance implications, is accounted for.

Django 3.2 has just been released including the capabilities needed to use async
patterns in the execution of views via coroutines. The usage of async await
would be very beneficial for performance in both events and filters that are I/O
heavy. The mechanism for calling filters should be implemented in a way that
makes usage of this pattern possible. For the case of events, we expect Django
to already implement their signals in the best possible way.

Distributing the business logic into separate repos that are finally run in the
same process, might be difficult to debug. The implementation of this proposal
should include tools to track performance data as well as a glimpse into the
current receivers of an event.


Use Cases
=========

To further explain the sort of use cases that this proposal makes possible, we
propose certain examples made easy by this framework.

#. Generating a certificate in a third party system

   The Event that fires after a certificate is generated in the platform is the
   perfect place to call upon a service such as https://www.accredible.com/ or
   https://www.sertifier.com to generate an external certificate. The same
   applies to a badge in any badging system.

#. Making enrollment possible for "premium" users in all courses

   A Filter right before the enrollment could check the user metadata or call an
   external system to determine if they are in a business tier that allows a
   paying user to enroll in any course for an online academy. This use case
   would require the hook called before the enrollment to support halting the
   process as one of its responses to what the filter returns.

#. Adding context information for an enterprise customer to a certificate

   When rendering a certificate, a particular template that is stored in the
   database may require certain information that is only available for users of
   the particular course the certificate belongs to. This very custom logic can
   be separated from the core platform repository via a Filter.

Refer to `openedx-events-2-zapier`_ and `openedx-filters-samples`_ for the implementation
of some basic use cases.

.. _openedx-events-2-zapier: https://github.com/eduNEXT/openedx-events-2-zapier
.. _openedx-filters-samples: https://github.com/eduNEXT/openedx-filters-samples

Backward Compatibility
**********************

The only current extension point that could be affected by this OEP is the
REGISTRATION_EXTENSION_FORM. We suggest that this extension point continues to
be supported until it is clear that migrating this to hooks is possible, and the
migration path to the corresponding hook is well defined and documented.


Reference Implementation
************************

The current implementation can be found at:

* The libraries repositories: `Open edX Events`_ and `Open edX Filters`_
* PRs including the library and using it to define the latest hooks: https://github.com/openedx/edx-platform/pulls?q=is%3Apr+%22BD-32%22

.. _Open edX Events: https://github.com/openedx/openedx-events
.. _Open edX Filters: https://github.com/openedx/openedx-filters

Rejected Alternatives
*********************

* Django Signals was initially considered as the primary mechanism to connect both events and filters. It was eventually discarded for the filters part of the framework due to the lack of ordering capabilities required for the reducing of results when multiple functions are registered in a filter.

* It was considered to split this OEP into two. One handling the hooks that are of the event kind and then a second handling the filter kind. This was however rejected fearing that the proposal could be stuck only after the first iteration and never reach its full potential. Instead, a phased implementation of hooks from both kinds will be pursued.

* Adding celery directly in the tooling in order to make the calling of async tasks as trivial as a configuration flag was initially considered. This added extra dependencies at several locations and did not resolve the general issue of serializing some complex objects such as requests and Django Users.


References
**********

This OEP was first discussed and presented to the community via `a public google doc`_.
The discussion held there is included into the present proposal.

A thread in the `openedx discourse server`_ served as the main feedback loop.

.. _a public google doc: https://docs.google.com/document/d/1jhnudz6AVtVt0ZSRSwOwj9gJ0lsDDn_8mUCPehLPzLw/edit#
.. _openedx discourse server: https://discuss.openedx.org/t/configuration-for-the-hooks-extension-framework/4527


The current documentation for the Hooks Extension Framework can be found at `Open edX Guides Hooks`_ section.

.. _Open edX Guides Hooks: https://github.com/openedx/edx-platform/tree/master/docs/guides/hooks

.. note:: All decisions related to Open edX Events and Filters will now be documented and made publicly available through their respective Architectural Decision Records (ADRs). For more information, see the :ref:`Open edX Events ADRs <openedx-events:ADRs>` and :ref:`Open edX Filters ADRs <openedx-filters:ADRs>`.

Change History
**************

14 January 2025 - Maria Grimaldi
Added note about decisions related to Open edX Events and Filters being documented and made publicly available through the Architectural Decision Records (ADRs) in the `openedx-events <https://github.com/openedx/openedx-events>`_ and `openedx-filters <https://github.com/openedx/openedx-events>`_ repositories.

20 July 2022 - Maria Grimaldi
Update OEP-50 with latest documentation.

17 April 2021 - Felipe Montoya
Updating based on feedback from the community and edX arch team.

24 March 2021 - Felipe Montoya
Adding an official arbiter

04 February 2021 - Felipe Montoya
Converted to Draft OEP

28 July 2020 - Felipe Montoya.
Initial version made public for the community at the google docs file.
