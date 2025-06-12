.. _oep-52:
.. _OEP-52 Event Bus Architecture:

OEP-52: Event Bus Architecture
##############################

.. This OEP template is based on Nygard's Architecture Decision Records.

.. list-table::
   :widths: 25 75

   * - OEP-52
     - :ref:`OEP-52 <OEP-52 Event Bus Architecture>`
   * - Title
     - Event Bus Architecture
   * - Last Modified
     - 2024-07-09
   * - Authors
     - Feanil Patel <feanil@edx.org>, Robert Raposa <rraposa@edx.org>
   * - Arbiter
     - Tobias Macey <tmacey@mit.edu>
   * - Status
     - Accepted
   * - Type
     - Architecture
   * - Created
     - 2021-08-16
   * - Review Period
     - 2022-06-09 - 2022-06-30
   * - References
     - Follow up work: `event bus project roadmap`_

Overview
********

* Adding an event bus to the Open edX platform allows asynchronous event messaging across services, which enables a number of improvements aligned with our architectural goals.

* An abstraction layer will be provided to enable the choice of multiple technologies for implementing an Open edX event bus.

* Kafka and Redis streams implementations of the event bus have been implemented.

* Event bus events extend the Hooks Extension Framework events, and use an Avro schema to serialize the existing hooks signals.

.. _event bus project roadmap: https://github.com/openedx/platform-roadmap/issues/28

Context
*******

The already accepted :ref:`OEP-41 Asynchronous Server Event Message Format` details the general format and conventions the Open edX platform should use for asynchronous event messaging across services. It also provides background on a set of :ref:`Event Messaging Architectural Goals` for the Open edX platform, including:

* Align with the `Architecture Manifesto`_ themes of decentralization and asynchronous communication in support of loose coupling between services.
* Eliminate blocking, synchronous requests.
* Eliminate expensive, batch synchronization.
* Reduce the need for plugins.
* Flexibly integrate with event producers.
* Simplify integration to external systems.

However, this earlier OEP explicitly leaves out of scope the specific transport and libraries used for this messaging. At the time, the Open edX platform lacked a reliable way to send events to multiple consumers across services, following the `publish-subscribe messaging pattern`_ (pub/sub).


.. _Architecture Manifesto: https://openedx.atlassian.net/wiki/spaces/AC/pages/1074397222/Architecture+Manifesto+WIP
.. _publish-subscribe messaging pattern: https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern

Decision
********

* Adding an event bus to the Open edX platform allows asynchronous event messaging across services, which enables a number of improvements aligned with our architectural goals.

* For the purpose of this OEP, the focus of the event bus is to provide `publish-subscribe messaging pattern`_ (pub/sub) and event-driven capabilities.

* An abstraction layer will be provided to enable the choice of multiple technologies for implementing an Open edX event bus.

  * See `ADR on Multiple Event Types Per Topic`_ to learn more about the requirement of Open edX event bus implementations to support multiple event types per topic.

* An initial event bus implementation has been implemented using Kafka.

  * See edx.org `ADR on Kafka-Based Event Bus`_ when considering other event-bus technologies.
  * See edx.org `ADR on Kafka Managed Hosting`_ if considering Kafka hosting options.
  * See edx.org `ADR on Managing Kafka Consumers`_ if exploring how to deploy consumers for Kafka.

* A secondary implementation has been implemented using Redis streams.

  * See `ADR on Redis streams Event Bus`_.

* Event bus events extend the Hooks Extension Framework events, and use an Avro schema to serialize the existing hooks signals.

  * See `ADR on External event bus and Django Signal events`_ to learn more about how the ``OpenEdxPublicSignal`` internal used to send the same internal signal-based events across services, and then fire the signal-based events again within consuming services.
  * See `ADR on External Event Schema Format`_ and `ADR on Event Schema Serialization and Evolution`_ to learn about how the Avro Schema format is used for serializing external events published to Kafka, and consumed from Kafka. Additionally, learn about tooling to automatically handle the translation from ``OpenEdxPublicSignal`` internal events to Avro Schema formatted events, and back again. Also learn how this explicit schema format can aid in schema evolution.

.. _ADR on Multiple Event Types Per Topic: https://openedx-events.readthedocs.io/en/latest/decisions/0010-multiple-event-types-per-topic.html

.. _ADR on Kafka-Based Event Bus: https://github.com/openedx/event-bus-kafka/blob/main/docs/decisions/0002-kafka-based-event-bus.rst
.. _ADR on Kafka Managed Hosting: https://github.com/openedx/event-bus-kafka/blob/main/docs/decisions/0004-kafka-managed-hosting.rst
.. _ADR on Managing Kafka Consumers: https://github.com/openedx/event-bus-kafka/blob/main/docs/decisions/0003-managing-kafka-consumers.rst

.. _ADR on Redis streams Event Bus: https://github.com/openedx/event-bus-redis/blob/main/docs/decisions/0002-redis-streams-based-event-bus.rst

.. _ADR on External event bus and Django Signal events: https://openedx-events.readthedocs.io/en/latest/decisions/0004-external-event-bus-and-django-signal-events.html
.. _ADR on External Event Schema Format: https://openedx-events.readthedocs.io/en/latest/decisions/0005-external-event-schema-format.html
.. _ADR on Event Schema Serialization and Evolution: https://openedx-events.readthedocs.io/en/latest/decisions/0006-event-schema-serialization-and-evolution.html

.. note:: All decisions related to Open edX Events where the event bus key components are implemented will now be documented and made publicly available through the Architectural Decision Records (ADRs) in the `openedx-events <https://github.com/openedx/openedx-events>`_ repository. For more details, refer to the :doc:`Open edX Events ADRs <openedx-events:decisions/index>`.

Consequences
************

* Individual usage of any new Open edX event bus cannot be required until there is an implementation available as part of a named release of the Open edX platform.

* Offering an abstraction layer for the event bus is the current intent, but will require additional work and support. Additionally, any alternative technology implementations (in addition to Kafka and Redis) will require implementation and support.

* Although external events offer many benefits, it also requires a different mindset around eventual consistency. Proper education and experience will be needed to support the success of the event bus.

Change History
**************

2025-01-14
==========

* Added note about decisions related to Open edX Events being documented and made publicly available through the Architectural Decision Records (ADRs) in the `openedx-events <https://github.com/openedx/openedx-events>`_ repository.

2024-07-09
==========

* Removed note about OEP being provisional, as it has been marked as Accepted.
* `Pull request #605 <https://github.com/openedx/open-edx-proposals/pull/605>`_

2023-05-15
==========

* Added link to event_bus_redis implementation ADR.
* Updated OEP status to Accepted.
* `Pull request #477 <https://github.com/openedx/open-edx-proposals/pull/477>`_

2023-05-12
==========

* Added link to new openedx-events ADR
* `Pull request #478 <https://github.com/openedx/open-edx-proposals/pull/478>`_

2022-11-29
==========

* Removed disclaimer about provisional ADRs (most have since been accepted) and added follow-up work in References
* `Pull request #414 <https://github.com/openedx/open-edx-proposals/pull/414>`_

2022-07-11
==========

* Updated formatting.
* Updated note about provisional updates, including reference to roadmap item.
* `Pull request #351 <https://github.com/openedx/open-edx-proposals/pull/351>`_

2022-03-30
==========

Updated draft as follows:

* Moved the Kafka technology choice to a separate edx.org ADR.
* Added references and overview of a variety of other event bus related ADRs.
* Moved ADR on schema representation to openedx-events, near a number of other event bus related ADRs.
* `Pull request #324 <https://github.com/openedx/open-edx-proposals/pull/324>`_

2021-08-16
==========

Initial draft.
