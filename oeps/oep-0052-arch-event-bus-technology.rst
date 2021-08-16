==============================
OEP-0052: Event Bus Technology
==============================

.. This OEP template is based on Nygard's Architecture Decision Records.

.. list-table::
   :widths: 25 75

   * - OEP-52
     - :doc:`OEP-0052 </oeps/oep-0052-arch-event-bus-technology>`
   * - Title
     - Event Bus Technology
   * - Last Modified
     - 2021-08-16
   * - Authors
     - Feanil Patel<feanil@edx.org>
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

Context
-------

Open edX is a system of many services that need to talk to each other. Currently the best way we have to sync data
between these services is via a combination of Jenkins jobs and management commands. This has the following
shortcomings:

    - Work is batched and can't easily be done in near real-time. (Courseware metadata refresh)

    - Often requires a re-sync everything approach as opposed to incremental updates (Credentials?)

    - Adding new consumers or down stream services requires upstream changes(Not easily extensible)

Decision
--------

Open edX will benefit from having a message bus. We believe apache pulsar is a good choice for the bus and will
begin trialing it to solve some specific platform problems.

Consequences
------------

* Code to interact with pulsar and its libraries will be added to core services.

Use-Cases for a Message Bus: Once we have a message bus, what will we use it for?

    - All course/program updates propogation.

    - Feed into xAPI/Caliper capabilities.

    - Now services and features can be built fully de-coupled from the core application.

This section describes the resulting context, after applying the decision.
All consequences should be listed here, not just the "positive" ones. A particular
decision may have positive, negative, and neutral consequences, but all of them
affect the team and project in the future.

Rejected Alternatives
---------------------


Kafka
~~~~~

Pros
^^^^


Cons
^^^^
    * Requires Partition re-balancing to scale consumer groups.

    * Can lose acked data.(`1`_)

.. _1: https://jack-vanlightly.com/blog/2018/9/14/how-to-lose-messages-on-a-kafka-cluster-part1



Redis
~~~~~

Pros
^^^^


Cons
^^^^
    * Can lose acked data.

    * Would need to build our own schema management.


Abstract Message Bus Class
~~~~~~~~~~~~~~~~~~~~~~~~~~

Pros
^^^^


Cons
^^^^

References(TODO)
----------

* Convert the Rubric Definition and Evaluation to RST and put alongside OEP.

List any additional references here that would be useful to the future reader.
See `Documenting Architecture Decisions`_ for further input.

.. _Documenting Architecture Decisions: https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions

