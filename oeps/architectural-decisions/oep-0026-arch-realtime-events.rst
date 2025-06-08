.. _OEP-26 Real-time Events:

OEP-26: Real-time Events
##########################

+-----------------+--------------------------------------------------------+
| OEP             | :ref:`OEP-26 Real-time Events`                         |
+-----------------+--------------------------------------------------------+
| Title           | Real-time Events                                       |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2019-01-16                                             |
+-----------------+--------------------------------------------------------+
| Authors         | - Nimisha Asthagiri <nimisha@edx.org>,                 |
|                 | - Danial Malik <danial.malik@edly.io>,                 |
|                 | - Ed Zarecor <ed@axim.org>                             |
+-----------------+--------------------------------------------------------+
| Arbiter         | Ty Hob <ty@axim.org>                                   |
+-----------------+--------------------------------------------------------+
| Status          | Accepted                                               |
+-----------------+--------------------------------------------------------+
| Type            | Architecture                                           |
+-----------------+--------------------------------------------------------+
| Created         | 2018-07-11                                             |
+-----------------+--------------------------------------------------------+
| Last Updated    | 2025-02-05                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | 2018-11-29 - 2018-12-20                                |
+-----------------+--------------------------------------------------------+


.. contents::
   :local:
   :depth: 3

Status
******

As of 2025-02-05, this OEP has been accepted. It has been implemented in Open edX as the `event_routing_backends`_ plugin, which is currently in use for delivering real-time analytics to Aspects and to deliver real-time events to external systems via xAPI and Caliper. While that project adheres closely to this OEP, there are also other event-based communication mechanisms within the platform that are covered by other OEPs.

:ref:`OEP-41 <OEP-41 Asynchronous Server Event Message Format>` and :ref:`OEP-52 <oep-52>` cover the general format and conventions for generic asynchronous event messaging across and within services. While this OEP concerns itself learner-specific event sharing. There is also some overlap, for instance when event-routing-backends is configured to send events to an event bus, however this OEP is not considered superceded at this time.

Where possible this document has been updated to match the implemented reality. However some sections may be out of date. The `event_routing_backends`_ plugin is the authoritative source for the current implementation.

.. _event_routing_backends: https://github.com/openedx/event-routing-backends/

Context
*******

The Open edX system captures LMS interactions in persisted event streams (tracking logs) of documented proprietary JSON structures, as specified in `Events in the Tracking Logs`_. The events are emitted by backend servers and frontend apps, including web and mobile apps. The logs are shared and mostly used *asynchronously* for various data analyses and processing, though through different log handlers / forwarders they can be used for more real-time applications.

There are emerging use cases that require notifying external systems of these LMS events in real-time, preferably in industry standard formats (such as xAPI_ and Caliper_). By **real-time** events, we mean pushing data as fast as possible in an automated and synchronous communication at a speed within a few hundred milliseconds (to satisfy `human perception`_). Use cases, such as Adaptive Learning, do require this sub-second communication time. For other use cases, however, **near-real-time** communication (with a delay of multiple minutes) is sufficient. Unless explicitly called out in the document, near-real-time is included when we refer to real-time. Both are orders of magnitude faster than the **batch processing** of tracking logs.

This OEP proposes an evolutionary change to Open edX's eventing system that will enable near realtime event-based feedback loops.  Events have the potential to connect users, organizations, and learning services in ways that enrich learning on the platform.

This document describes a design proposal for supporting standard real-time events in the Open edX system.

.. _Events in the Tracking Logs: https://docs.openedx.org/en/latest/developers/references/internal_data_formats/index.html
.. _xAPI: https://xapi.com/
.. _Caliper: https://www.imsglobal.org/activity/caliper
.. _human perception: https://www.pubnub.com/blog/how-fast-is-realtime-human-perception-and-technology/


Use cases
=========

Although the remainder of this section enumerates some of the immediate use cases, for the time being we will focus our efforts on the needs for adaptive learning, while bearing in mind that the general framework for real-time events will be extended and reused.

Use Case: Adaptive Learning
---------------------------

**Requirement:** Real-time communications (*sub-second*)

As captured in `Open edX Adaptive Learning`_, adaptive learning in Open edX will enable the platform to respond to a learner's interactions in real-time,  automatically providing the learner with individualized support. Data analysis from earlier experimental attempts on edx.org and elsewhere show how adaptive learning mechanisms can significantly improve the learner's efficiency, engagement, and retention, while providing a more effective learning experience with metacognitive support for life-long learning.

.. _Open edX Adaptive Learning: https://openedx.atlassian.net/wiki/spaces/AC/pages/542343170/Adaptive+Learning

Integration with Adaptive Engines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As an iterative path to this future, the immediate goal is to enable integrations with external adaptive engines. Currently we are focused on establishing standard APIs that (1) adaptive engines can use to receive real-time events from the Open edX LMS, (2) the Open edX LMS can use to query adaptive engines on what to present to learners, and (3) train adaptive engines based on standard
events collected from multiple sources. This document focuses on #1 with the need to send scalable and real-time updates to adaptive engines. This is depicted by the left-hand flow in the diagram below. #3 is described in :ref:`oep-26-lrs`.

.. image:: oep-0026/adaptive_learning_lms_basic.png
   :alt: The left-hand side of the data flow diagram shows how the Open edX LMS sends events to an intermediary "Eventing" component, which forwards those events to adaptive engines. The right-hand side shows how the Open edX LMS queries the adaptive engines for "what's next" via an intermediary "Adaptive Experiences" component.

Use Case: Enterprise
--------------------

**Requirement:** Near-real-time communications (*minutes*)

As an `integration option for Enterprise clients`_, real-time events allow the Open edX system to immediately notify Enterprises of high-interest learning activities, such as course enrollments and completions, for learners in their organizations. Note that typically Enterprises favor xAPI_ as their standard of choice.

.. _integration option for Enterprise clients: https://openedx.atlassian.net/wiki/spaces/SOL/pages/532676633/Enterprise+xAPI+Implementation+Proposal

Use Case: University
--------------------

**Requirement:** Near-real-time communications (*minutes*)

Real-time events also allow the Open edX system to immediately notify Universities of their high-interest learning activities, such as unenrollments. Note that, in contrast to Enterprises, Universities typically favor Caliper_ as their standard of choice.

Use Case: Cross-LMS Integration
-------------------------------

**Requirement:** Near-real-time communications (*minutes*)

Another emerging use case is allowing unification of events across multiple LMS's. As a specific example, an organization that uses multiple Open edX instances (as multiple options for their learners or for distributing their reusable content) may want to accumulate all their LMS events in a single `Learning Record Store`_ that is then queried in real-time.

.. _Learning Record Store: https://xapi.com/learning-record-store/

Use Case: External Service
--------------------------

**Requirement:** Real-time (*sub-second*) or near-real-time (*minutes*) communications depending on integration type

As we move towards creating a more extensible platform for Open edX, we want to provide integration options between the Open edX platform and external services. External services that enhance the learning, authoring, analytics, or LMS administrative experiences will benefit greatly from a real-time eventing framework. Some examples include an external social learning service and a learning analytics tool. *Theoretically, the other use cases are specific cases of this* *use case*.

.. image:: oep-0026/use_cases.png

Technical Specification
***********************

This section provides some of the technical details of the design proposal.

Standards
=========

In order to enable a plug-n-play ecosystem with various external services (adaptive engines or otherwise), we will support both the ADL_'s `Experience API (xAPI)`_ standard and the `IMS Global`_'s `Caliper Sensor APIs`_ standard as the communication protocol between the Open edX LMS and registered external services.

.. _ADL: https://adlnet.gov/
.. _Experience API (xAPI): https://www.adlnet.gov/research/performance-tracking-analysis/experience-api/
.. _IMS Global: https://www.imsglobal.org/
.. _Caliper Sensor APIs: https://www.imsglobal.org/caliper-analytics-v1-public-repos-sensor-apis

xAPI Integration
----------------

For details on integrating with xAPI, please see the :ref:`xapi_realtime_events` design document.

Caliper Integration
-------------------

For details on integrating with Caliper, please see the :ref:`caliper_realtime_events` design document.

.. toctree::
    :hidden:

    oep-0026/caliper-realtime-events.rst
    oep-0026/xapi-realtime-events.rst


.. _oep-26-user-id:

Anonymized User ID
==================

By default users will be identified to external systems using a UUID that is associated uniquely with a single user and the external system type with which the UUID can be shared.  This decision overrides :ref:`oep-32` and is captured in `ADR 0001-externalid.rst`_. Some external systems may require a different identifier for the user to support, for example, xAPI specifies a `mbox` field that is an email address. The system will allow for the configuration of a different identifier for each external system type to allow for flexibility of integration with external systems.

.. _ADR 0001-externalid.rst: https://github.com/openedx/edx-platform/blob/e1c8fb51bec2be3aa8da9750cb52b01728d1a740/openedx/core/djangoapps/external_user_ids/docs/decisions/0001-externalid.rst


Eventing Components
===================

While the exact technical framework and tools for supporting real-time events *at scale* is to be determined, we can begin with implementing the various components of the eventing service from a modular perspective so they can be adopted into any scalable infrastructure (e.g., an Apache framework). So while we propose a framework in this section, each subcomponent is expected to be independent and modular so it can be recomposed as needed.

The proposed framework integrates into and builds upon the features of the `Open edX Event Tracking`_ library. The library's RoutingBackend_ provides powerful and flexible tools with its two fundamental building blocks of `processors and backends`_. The diagram below depicts a possibility of using these tools to implement our real-time Eventing subsystem.

.. _Open edX Event Tracking: https://github.com/openedx/event-tracking
.. _RoutingBackend: https://github.com/openedx/event-tracking/blob/03bedd4c4f269c65f266f7e95621a9c1b91f908d/eventtracking/backends/routing.py#L11
.. _processors and backends: https://github.com/openedx/event-tracking/blob/03bedd4c4f269c65f266f7e95621a9c1b91f908d/eventtracking/backends/routing.py#L16-L30

.. image:: oep-0026/eventing_subsystem.png

Here is a description of each subcomponent in the Eventing subsystem:

1. `Synchronous Real-time Eventing Backend`_: A pluggable component that is configured as a new tracking backend in the LMS and runs within the LMS process.
2. `Asynchronous Real-time Eventing Routing Backend`_: Asynchronously routes to each configured communication protocol (e.g., xAPI and Caliper).
3. `Filter Processor`_: Owns the business logic for access control and filtering of events. A common implementation for the Filter may be shared across communication protocol backends.
4. `Translator Processor`_: Translates Open edX native events to standardized Open edX events. Each communication protocol would have its own Translator component implementation.
5. `Router`_: Routes real-time translated, validated, filtered events to all registered consumers.
6. `Admin UI`_: Provides an administrative interface to configure and manage registered consumers.

Synchronous Real-time Eventing Backend
--------------------------------------

The **Synchronous Real-time Eventing Backend** serves as an integration point between the Open edX system and the rest of the Eventing subsystem. It plugs into the `Open edX Event Tracking`_ framework and runs within the LMS Process.

Asynchronous Real-time Eventing Routing Backend
-----------------------------------------------

The **Asynchronous Real-time Eventing Routing Backend** is a RoutingBackend_ component type that executes in an asynchronous process. It is configured to support multiple backends - one for each communication protocol. For example, there would be a communication backend for Caliper and another one for xAPI.

Filter Processor
----------------

The **Filter Processor** component serves multiple purposes:

1. Preemptively optimizes further operations downstream by filtering only those events that are needed to be communicated, according to configuration settings, prior to enqueuing the events onto the `Asynchronous Real-time Eventing Routing Backend`_.

2. Provides APIs to support an admin configuration interface.

3. Enforces access control by filtering events and appropriately emitting them through the `Router`_.

The complexity of this component may increase over time as we support (a) higher degrees of customization by consumers, such as filtering for only certain types of events, (b) varying degrees of permissions to activities, and (c) multi-tenancy requirements with consumers and providers associated with different organizations.

For the first iteration, we need the following permissions:

* **Course restriction** - access events only in certain courses.

In the future, we may need the following:

* **User restriction** - access all events for certain users.
* **Site restriction** - access events of certain sites.
* **Activity type restriction** - access certain types of events.

Translator Processor
--------------------

Each communication protocol has its own Translator component. This component is responsible for translating from an Open edX event schema to the schema for the target communication protocol.

.. _oep-26-validator:

Router
------

The router forwards events to interested consumers, using the target communication protocol.

Admin UI
--------

The administration component is responsible for allowing the Open edX site administrator to configure the list and permissions of registered event consumers.

Eventually, the registry of consumers may become a self-service portal where consumers may initiate their request for access. However, initially, we can simply store the settings as `Django Settings`_ since they will be easier for site-administrators to manage.  Any database storage of the routing rules can be done as a part of self-service portal work.

.. _Django Settings: https://docs.djangoproject.com/en/3.0/topics/settings/

Decisions & Consequences
************************

* **Event-driven APIs at Scale** - The Context_ section describes the motivation and recent use cases for supporting real-time events. This capability has the potential to dramatically improve how external services can integrate and extend the Open edX platform.  Real-time events will provide a mechanism for loosely coupled integrations with a reliable, standardized contract.

* **Implementation of an LRS is out of scope** - As mentioned in :ref:`oep-26-lrs`, creating an Open edX specific LRS not planned.

  As a consequence, adaptive engines may need to maintain their own LRS if they need to refer back to previous events. Given our business research to date, it seems many adaptive engines are already maintaining their own custom-optimized storage of event data.

Related Open edX Frameworks
***************************

Here are a list of current Open edX frameworks that are related to "eventing" but have different purpose or scope.

* **Event tracking** - The current `event-tracking library`_ captures events that are fired using its tracker_ (Python) APIs and routes them to all configured pre-processors and backends. Current core backends include a MongoBackend_ (persisted database), a LoggerBackend_ (a.k.a., "tracking  logs" of persisted files and SegmentBackend_ (external service called segment.com_ that collects and routes events).

  This OEP makes use of the `event-tracking library`_ as an integration point with the rest of the Open edX platform, and proposes a new backend that supports real-time delivery of events.

.. _event-tracking library: https://github.com/openedx/event-tracking
.. _tracker: https://event-tracking.readthedocs.io/en/latest/user_guide/design.html
.. _LoggerBackend: https://github.com/openedx/event-tracking/blob/d32d2b1ea7690c6710abd1060495c239f6809133/eventtracking/backends/logger.py#L15
.. _MongoBackend: https://github.com/openedx/event-tracking/blob/d32d2b1ea7690c6710abd1060495c239f6809133/eventtracking/backends/mongodb.py#L16
.. _SegmentBackend: https://github.com/openedx/event-tracking/blob/d32d2b1ea7690c6710abd1060495c239f6809133/eventtracking/backends/segment.py#L12
.. _segment.com: https://segment.com/

* **Event-driven microservices architecture** - Using event-driven asynchronous messaging between microservices is a design pattern we have adopted for the Open edX system. Such a loosely coupled architecture provides optimal scalability, resiliency and responsiveness, per recommendations in the `Reactive Manifesto`_.

  As summarized in Pivotal's `Messaging Patterns for Event-Driven Microservices`_, there are various integration frameworks for supporting eventing frameworks. By default we currently use the "Asynchronous Command Calls" (2nd) pattern via `Django Celery`_. As of February of 2025 both event-tracking and event-routing-backends can be configured to use the Kafka or Redis event bus implementations for sending real-time events as defined in :ref:`oep-52`.

  However, the requirements for that infrastructure are different from those captured in this OEP. This OEP requires support for standardized protocol(s) and configuration and access control (including PII considerations) for external consumers. Although it is theoretically possible for microservices to use this OEP's framework for inter-communication, it is not the intention. Events exchanged between microservices are within a firewall and can use non-standard schemas.

.. _Reactive Manifesto: https://www.reactivemanifesto.org/
.. _Messaging Patterns for Event-Driven Microservices: https://content.pivotal.io/blog/messaging-patterns-for-event-driven-microservices
.. _Django Celery: https://docs.celeryproject.org/en/latest/django/

* **Notifications and messaging framework** - It is also not the intention of this OEP's real-time eventing framework to support real-time messaging to users. The Open edX `Automated Communication Engine (ACE)`_ is a Django library that supports personalized delivery of user-targeted messages. It is a pluggable and modular framework that supports multiple delivery channels with theme-aware and user-language-aware message templates.

  Although it is possible for this OEP's real-time eventing framework to send events targeted to IoT and personal devices, those events will not be translated nor customized for each individual recipient, nor be adaptive to the individual's policies and time sensitivities. ACE would be a better alternative for those requirements.

.. _Automated Communication Engine (ACE): https://edx-ace.readthedocs.io/en/latest/index.html

Change History
**************

2025-02-05
==========

* Mark as accepted
* Update to reflect current implementation
* `PR #678 <https://github.com/openedx/open-edx-proposals/pull/678>`_

2023-11-06
==========

* Fixed various typos
* `PR #537 <https://github.com/openedx/open-edx-proposals/pull/537>`_

2023-04-14
==========

* Replace tCRIL references with Axim references
* `PR #464 <https://github.com/openedx/open-edx-proposals/pull/464>`_

2022-11-04
==========

* Change Ed Zarecor from arbiter to author, add Brian Mesick as Arbiter
* Clarify language and update to reflect current thoughts
* Remove references to Validator Processor
* Change implementation of an LRS from "Deferring implementation" to "Out of scope"
* `PR #405 <https://github.com/openedx/open-edx-proposals/pull/405>`_

2022-09-27
==========

* Change arbiter from Brian Wilson to Ed Zarecor
* `PR #386 <https://github.com/openedx/open-edx-proposals/pull/386>`_

2022-09-16
==========

* Change Github organization references from edx to openedx
* `PR #376 <https://github.com/openedx/open-edx-proposals/pull/376>`_

2021-01-20 - 2022-08-02
=======================

* RsT style cleanup and minor content updates
* `PR #365 <https://github.com/openedx/open-edx-proposals/pull/365>`_
* `PR #248 <https://github.com/openedx/open-edx-proposals/pull/248>`_
* `PR #245 <https://github.com/openedx/open-edx-proposals/pull/245>`_
* `PR #206 <https://github.com/openedx/open-edx-proposals/pull/206>`_

2021-04-09
==========

* Change http URLs to https
* `PR #200 <https://github.com/openedx/open-edx-proposals/pull/200>`_

2021-01-20
==========

* Clarify when we should build on Django admin vs settings or a self-service portal
* `PR #156 <https://github.com/openedx/open-edx-proposals/pull/156>`_

2020-12-07
==========

* Update example code
* `PR #175 <https://github.com/openedx/open-edx-proposals/pull/175>`_

2020-09-20 - 2020-09-30
=======================

* Cleaning up links and references
* `PR #162 <https://github.com/openedx/open-edx-proposals/pull/162>`_
* `PR #163 <https://github.com/openedx/open-edx-proposals/pull/163>`_

2020-06-04
==========

* Significant updates to Caliper documentation
* `PR #152 <https://github.com/openedx/open-edx-proposals/pull/152>`_

2019-05-25
==========

* Update title to remove prefixing zeros for consistency
* `PR #115 <https://github.com/openedx/open-edx-proposals/pull/115>`_

2019-05-15
==========

* Update anonymized user ID section to unique user id as part of adding OEP-32
* `PR #103 <https://github.com/openedx/open-edx-proposals/pull/103>`_

2019-01-19
==========

* Initial publication
* `PR #73 <https://github.com/openedx/open-edx-proposals/pull/73>`_
