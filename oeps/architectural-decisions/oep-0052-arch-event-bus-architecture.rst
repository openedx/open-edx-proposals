================================
OEP-0052: Event Bus Architecture
================================

.. This OEP template is based on Nygard's Architecture Decision Records.

.. list-table::
   :widths: 25 75

   * - OEP-52
     - :doc:`OEP-0052 <oep-0052-arch-event-bus-architecture>`
   * - Title
     - Event Bus Architecture
   * - Last Modified
     - 2022-03-30
   * - Authors
     - Feanil Patel <feanil@edx.org>, Robert Raposa <rraposa@edx.org>
   * - Arbiter
     - Tobias Macey <tmacey@mit.edu>
   * - Status
     - Under Review (=> Provisional)
   * - Type
     - Architecture
   * - Created
     - 2021-08-16
   * - Review Period
     - 2022-06-09 - 2022-06-30

Overview
--------

.. note:: Consider this OEP an early draft to provide intent and gather feedback. The language and content of the OEP will change over time as we progress on this work and hammer out certain decisions.

* Adding an event bus to the Open edX platform allows asynchronous event messaging across services, which enables a number of improvements aligned with our architectural goals.

* An abstraction layer will be provided to enable the choice of multiple technologies for implementing an Open edX event bus.

* An initial event bus implementation is being implemented using Kafka.

* Event bus events will further extend the Hooks Extension Framework events, and use an Avro schema to serialize the existing hooks signals.


Context
-------

The already accepted :doc:`oep-0041-arch-async-server-event-messaging` details the general format and conventions the Open edX platform should use for asynchronous event messaging across services. It also provides background on a set of :ref:`Event Messaging Architectural Goals` for the Open edX platform, including:

* Align with the `Architecture Manifesto`_ themes of decentralization and asynchronous communication in support of loose coupling between services.
* Eliminate blocking, synchronous requests.
* Eliminate expensive, batch synchronization.
* Reduce the need for plugins.
* Flexibly integrate with event producers.
* Simplify integration to external systems.

However, this earlier OEP explicitly leaves out of scope the specific transport and libraries used for this messaging. At this time, the Open edX platform still lacks a reliable way to send events to multiple consumers across services, following the publish-subscribe (pub/sub) messaging pattern.

In other words, we have documented what we wish to do and why, but we do not yet fully have the capability to do it. We are missing a reliable event messaging infrastructure with `publish-subscribe messaging pattern`_ (pub/sub) capabilities.

.. _Architecture Manifesto: https://openedx.atlassian.net/wiki/spaces/AC/pages/1074397222/Architecture+Manifesto+WIP
.. _publish-subscribe messaging pattern: https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern

Decision
--------

* Adding an event bus to the Open edX platform allows asynchronous event messaging across services, which enables a number of improvements aligned with our architectural goals.

* For the purpose of this OEP, the focus of the event bus is to provide `publish-subscribe messaging pattern`_ (pub/sub) and event-driven capabilities.

* An abstraction layer will be provided to enable the choice of multiple technologies for implementing an Open edX event bus.

.. note:: All referenced ADRs are Provisional at this time, until we are able to confirm decisions through a production-validated implementation. **TODO:** Update references to edx.org ADRs as they move around.

* An initial event bus implementation is being implemented using Kafka.

  * See edx.org `ADR on Kafka-Based Event Bus`_ when considering other event-bus technologies.
  * See edx.org `ADR on Kafka Managed Hosting`_ if considering Kafka hosting options.
  * See edx.org `ADR on Kafka Consumers`_ if exploring how to deploy consumers for Kafka.

* Event bus events will further extend the Hooks Extension Framework events, and use an Avro schema to serialize the existing hooks signals.

  * See `ADR on External event bus and Django Signal events`_ to learn more about how the ``OpenEdxPublicSignal`` internal event will be used to send the same internal signal-based events across services, and then fire the signal-based events again within consuming services.
  * See `ADR on External Event Schema Format`_ and `ADR on Event Schema Serialization and Evolution`_ to learn about how the Avro Schema format will be used for serializing external events published to Kafka, and consumed from Kafka. Additionally, learn about tooling to automatically handle the translation from ``OpenEdxPublicSignal`` internal events to Avro Schema formatted events, and back again. Also learn how this explicit schema format can aid in schema evolution.

.. _ADR on Kafka-Based Event Bus: https://github.com/edx/edx-arch-experiments/blob/main/edx_arch_experiments/kafka_consumer/docs/decisions/0001_kafka_based_event_bus.rst
.. _ADR on Kafka Managed Hosting: https://github.com/edx/edx-arch-experiments/blob/main/edx_arch_experiments/kafka_consumer/docs/decisions/0003_kafka_managed_hosting.rst
.. _ADR on Kafka Consumers: https://github.com/edx/edx-arch-experiments/blob/main/edx_arch_experiments/kafka_consumer/docs/decisions/0002_kafka_consumer_command.rst

.. _ADR on External event bus and Django Signal events: https://openedx-events.readthedocs.io/en/latest/decisions/0004-external-event-bus-and-django-signal-events.html
.. _ADR on External Event Schema Format: https://openedx-events.readthedocs.io/en/latest/decisions/0005-external-event-schema-format.html
.. _ADR on Event Schema Serialization and Evolution: https://openedx-events.readthedocs.io/en/latest/decisions/0006-event-schema-serialization-and-evolution.html

Consequences
------------

* Individual usage of any new Open edX event bus cannot be required until there is an implementation available as part of a named release of the Open edX platform.

* Offering an abstraction layer for the event bus is the current intent, but will require additional work and support. Additionally, any alternative technology implementations (in addition to Kafka) will require implementation and support.

* Although external events offer many benefits, it also requires a different mindset around eventual consistency. Proper education and experience will be needed to support the success of the event bus.

Change History
--------------

2022-03-30: Updated draft as follows:

  * Moved the Kafka technology choice to a separate edx.org ADR.
  * Added references and overview of a variety of other event bus related ADRs.
  * Moved ADR on schema representation to openedx-events, near a number of other event bus related ADRs.

2021-08-16: Initial draft.