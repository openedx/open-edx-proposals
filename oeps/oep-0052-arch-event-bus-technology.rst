==============================
OEP-0052: Event Bus Technology
==============================

.. This OEP template is based on Nygard's Architecture Decision Records.

.. list-table::
   :widths: 25 75

   * - OEP-52
     - :doc:`OEP-0052 <oep-0052-arch-event-bus-technology>`
   * - Title
     - Event Bus Technology
   * - Last Modified
     - 2021-08-26
   * - Authors
     - Feanil Patel <feanil@edx.org>, Robert Raposa <rraposa@edx.org>
   * - Arbiter
     - TBD
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2021-08-16
   * - Review Period
     - TBD

Overview
--------

* Adding an event bus to the Open edX platform allows asynchronous event messaging across services, which enables a number of improvements aligned with our architectural goals.

* We are trialing Kafka to implement an Open edX event bus. This decision will be updated as we commit or change direction.

* *Note:* Although this infrastructure could serve multiple purposes in the future, this OEP is focused on `publish-subscribe messaging pattern`_ (pub/sub) capabilities.

Context
-------

The already accepted :doc:`oep-0041-arch-async-server-event-messaging` details the general format and conventions the Open edX platform should use for asynchronous event messaging across services. It also provides background on a set of :ref:`Event Messaging Architectural Goals` for the Open edX platform, including:

* Align with the `Architecture Manifesto`_ themes of decentralization and asynchronous communication.
* Eliminate blocking, synchronous requests.
* Eliminate expensive, batch synchronization.
* Reduce the need for plugins.
* Flexibly integrate with event producers.
* Simplify integration to external systems.

However, this earlier OEP explicitly leaves out of scope the specific transport and libraries used for this messaging. At this time, the Open edX platform still lacks a reliable way to send events to multiple consumers across services, following the publish-subscribe (pub/sub) messaging pattern.

In other words, we have documented what we wish to do and why, but we do not yet fully have the capability to do it. We are missing a reliable event messaging infrastructure with `publish-subscribe messaging pattern`_ (pub/sub) capabilities.

Note: The `Architecture Manifesto`_ mentions being inspired by the `Reactive Manifesto`_. Although there is a lot of overlap, this might also cause confusion, because the Reactive Manifesto discusses `Message Driven (in contrast to Event-Driven)`_, which they define as messages sent to a specific destination. However, this decision is not concerned with Message Driven by this definition, but instead about Event-Driven capabilities using pub/sub.

.. _Architecture Manifesto: https://openedx.atlassian.net/wiki/spaces/AC/pages/1074397222/Architecture+Manifesto+WIP
.. _Reactive Manifesto: https://www.reactivemanifesto.org/
.. _Message Driven (in contrast to Event-Driven): https://www.reactivemanifesto.org/glossary#Message-Driven

Decision
--------

The Open edX platform will benefit from having a message bus. We believe `Apache Kafka`_ is a good choice for the event bus and will begin trialing it to solve some specific platform problems addressed by the `publish-subscribe messaging pattern`_.

.. _Apache Kafka: https://kafka.apache.org/
.. _publish-subscribe messaging pattern: https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern

Why (trial) Kafka?
~~~~~~~~~~~~~~~~~~

Kafka has been around for a long time. See `Thoughtworks's technology radar introduced Kafka`_ as "Assess" in 2015, and "Trial" in 2016. It never moved up to "Adopt", and also never moved down to "Hold". Read `Thoughtwork's Kafka decoder page`_ to lear more about its benefits and trade-offs, and how it is used.

More recently, the `Thoughtworks's technology radar introduced Apache Pulsar`_ as "assess" in 2020, and the `technology radar introduced Kafka API without Kafka`_ in 2021. This both demonstrates near standard of the Kafka API, but also Thoughtwork's hope to find a less complex alternative.

This history closely parallels our own internal research and comparisons. We believe Apache Kafka is still the right option due to its maturity, documentation, support and community. However, Kafka can end up being a very complex collection of tools, some of which Apache Pulsar was designed to simplify. Therefore, we will begin a trial of Kafka, keeping in mind the potential benefits of Pulsar, and ultimately commit or start a trial of Pulsar.

This OEP will be adjusted as we learn more and make a final decision.

.. _Thoughtworks's technology radar introduced Kafka: https://www.thoughtworks.com/radar/tools/apache-kafka
.. _Thoughtwork's Kafka decoder page: https://www.thoughtworks.com/decoder/kafka

.. _Thoughtworks's technology radar introduced Apache Pulsar: https://www.thoughtworks.com/radar/platforms/apache-pulsar
.. _technology radar introduced Kafka API without Kafka: https://www.thoughtworks.com/radar/platforms/kafka-api-without-kafka

Messaging Features
~~~~~~~~~~~~~~~~~~

Kafka is a distributed streaming platform. Kafka's implementation maps nicely to the pub/sub pattern. However, some native features of a message broker like RabbitMQ are not built-in.

There is a useful `blog article comparing Kafka and RabbitMQ`_ by Eran Stiller. The article compares the technologies as pub/sub implementations across the following dimensions (winner in parentheses):

* Message Ordering (Kafka)
* Message Routing/Filtering (RabbitMQ)
* Message Timing (RabbitMQ)
* Message Retention (Kafka)
* Fault Handling (RabbitMQ)
* Scale (Kafka)
* Consumer Complexity (RabbitMQ)

Above dimensions which we ultimately require, but were won by RabbitMQ, will likely require additional development and/or supplementary technologies, as partially detailed in the next section.

Note: Some of these missing features are natively supported by `Apache Pulsar`_, at least according to its documentation.

.. _blog article comparing Kafka and RabbitMQ: https://stiller.blog/2020/02/rabbitmq-vs-kafka-an-architects-dilemma-part-2/

Kafka Add-ons, Distributions, and Providers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As part of the trial, there will be many additional related technologies to explore. Each technology affects the developer experience, the operator experience, or both. Some choices may ultimately affect the Open edX plaform for the entire community, and some choices may be unique to each organization (like edx.org). This document will ultimately contain details for both these cases, since they may help other organizations even when multiple options are still available.

The following is a list of just some of the potential technologies that may need to be deployed and managed:

* `Apache Kafka`_
* `Kafka Streams <https://kafka.apache.org/documentation/streams/>`__
* `Kafka Connect <https://kafka.apache.org/documentation/#connect>`__
* `Cruise Control <https://github.com/linkedin/cruise-control>`__
* `Faust <https://faust.readthedocs.io/en/latest/userguide/kafka.html>`__ (Python version similar to Kafka Streams)
* `Various Python clients <https://cwiki.apache.org/confluence/display/KAFKA/Clients#Clients-Python>`__

Note: `Amazon MSK`_ is an AWS managed service that supplies the Apache Kafka core platform only.

or

* `Confluent Platform`_ - Enterprise Kafka Distribution (Open Source, Community, or Commercial)

  * `Schema Registry <https://www.confluent.io/product/confluent-platform/data-compatibility/>`__
  * Monitoring and alerting capabilities (Commercial)
  * Self-balancing clusters (Commercial)
  * Tiered storage (Commercial) (future feature of Apache Kafka)
  * Infinite retention (Cloud only?)

Additional Notes:

* `Apache Pulsar`_ has similar features as part of its platform, which is why it makes a good potential alternative. However, the features are less battle-tested and the deployment story *may* be more complicated.
* Confluent also offers Confluent Cloud, a fully managed solution that offers much simpler operations, but is unlikely to be used by edX.org.

Also see a useful and biased `comparison of Apache Kafka vs Vendors`_ by Kai Waehner (of Confluent), comparing various providers and distributions of Kafka and related or competitive services.

.. _Amazon MSK: https://aws.amazon.com/msk/
.. _Confluent Platform: https://www.confluent.io/product/confluent-platform
.. _comparison of Apache Kafka vs Vendors: https://www.kai-waehner.de/blog/2021/04/20/comparison-open-source-apache-kafka-vs-confluent-cloudera-red-hat-amazon-msk-cloud/

Kafka Highlights
~~~~~~~~~~~~~~~~

Pros
^^^^

* Battle-tested, widely adopted, big community, lots of documentation and answers.
* Amazon MSK (AWS service) provides hosted path of least resistance.
* `New Relic integration with Amazon MSK`_ (useful to edX.org).

Cons
^^^^

* Many open questions about add-ons required for developers and operators.
* Complex to manage, including likely manual scaling.

.. _New Relic integration with Amazon MSK: https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-managed-kafka-msk-integration/

Consequences
------------

* Operators will need to deploy and manage the selected infrastructure, which is likely to be complex. If Apache Kafka is selected, there are likely to be a set of auxiliary parts to provide all required functionality for our message bus.
* Education will be required for both developers and operators regarding best practices for each role.
* Code to interact with Kafka and its libraries will be added to core services.
* At least one initial use case must be completed. One potential candidate is the grade change event in the LMS, and its use by the Credentials service.
* Once we have a message bus, we can investigate other potential use cases:

  * Course/program update propagation.
  * Feed into xAPI/Caliper capabilities.
  * New services and features can be built fully de-coupled from the core application.

Rejected Alternatives
---------------------

Apache Pulsar
~~~~~~~~~~~~~

Although rejected to start, `Apache Pulsar`_ remains an option if solving with Kafka turns out to be overly burdensome for developers or operators.

Pros
^^^^

* Ease of scalability (built-in, according to docs).
* Ease of data retention capabilities.
* Additional built-in pub/sub features (built-in, according to docs).

Cons
^^^^

* Requires 3rd party hosting or larger upfront investment in self-hosted (kubernetes).
* Less mature (but growing) community, little documentation, and few answers.

Note: Read an interesting (Kafka/Confluent) biased article exploring `comparisons and myths of Kafka vs Pulsar`_.

.. _Apache Pulsar: https://pulsar.apache.org/
.. _comparisons and myths of Kafka vs Pulsar: https://dzone.com/articles/pulsar-vs-kafka-comparison-and-myths-explored

Redis
~~~~~

Pros
^^^^

* Already part of Open edX platform

Cons
^^^^

* Can lose acked data, even if RAM backed up with an append-only file (AOF).
* Requires homegrown schema management.

Abstract Message Bus Class
~~~~~~~~~~~~~~~~~~~~~~~~~~

@feanil - What is this???

Pros
^^^^

Cons
^^^^

RabbitMQ
~~~~~~~~

Pros
^^^^

* Built-in message broker capabilities like routing, filtering, and fault handling.

Cons
^^^^

* Difficulties operating in the past. (Details???)
* Not built for message retention or message ordering.

Additional References
---------------------

* Technology comparisons performed by edX.org

  * `Message Bus Rubric Definition <https://docs.google.com/document/d/1lKbOE8HkUk__Cyy5u_yFZ8ju0roPtlxcH1-9yf9hX8I/edit#>`__

    * **TODO:** Move to Confluence or RST.
    * Includes many additional resources at bottom of document.

  * `Message Bus Evaluation <https://docs.google.com/spreadsheets/d/1pA08DQ1h3bov5fL1KTrT0tk2RJseyxPsZCLJACtb3YY/edit#gid=0>`__ (TODO: Move to Confluence or RST)

    * **TODO:** Move to Confluence or RST.

  * `Pulsar vs Kafka Hosting Comparison <https://openedx.atlassian.net/wiki/spaces/SRE/pages/3079733386>`__

* Third-party comparisons of Kafka vs Pulsar:

  * `(Kafka biased) Benchmarking comparison <https://www.confluent.io/blog/kafka-fastest-messaging-system/>`__
  * `(Pulsar biased) Performance, Architecture, and Features comparison - Part 1 <https://streamnative.io/en/blog/tech/2020-07-08-pulsar-vs-kafka-part-1/>`__
  * `(Pulsar biased) Performance, Architecture, and Features comparison - Part 2 <https://streamnative.io/en/blog/tech/2020-07-22-pulsar-vs-kafka-part-2/>`__
  * `(Kafka biased) Twitter's move from Pulsar-like to Kafka <https://blog.twitter.com/engineering/en_us/topics/insights/2018/twitters-kafka-adoption-story>`__

* Hosting comparisons:

  * `(Confluent biased) Amazon MSK vs Confluent Cloud <https://www.confluent.io/confluent-cloud-vs-amazon-msk>`__
