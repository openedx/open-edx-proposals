=============================================
OEP-41: Asynchronous Server Event Messaging
=============================================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-41 </oeps/oep-0041-arch-async-server-event-messaging.rst>`
   * - Title
     - Asynchronous Server Messaging
   * - Last Modified
     - 2020-02-23
   * - Authors
     - David Ormsbee <dave@edx.org>
   * - Arbiter
     - ?
   * - Status
     - Draft
   * - Type
     - Architecture (? Best Practice?)
   * - Created
     - 2020-02-23
   * - Review Period
     - ?
   * - Resolution
     - ?
   * - References
     - ?

Abstract
========

This OEP describes the conventions Open edX should use for asynchronous event
messaging across services. These events would be emitted when a service has
taken some action to data that it owns, and that other services would be
interested in. This could be a course publish, a user enrollment, updates to
catalog metadata, etc.

This OEP does not cover:

* Remote Prodecure Calls, i.e. using messaging to send commands to other
  services. Events in this document are the services broadcasting, "Here is what
  I just did!" to interested parties, not "Please do this thing for me!"
* Real-time streaming of student learning events to external platforms, as
  detailed in `OEP-26: Real-time Events`_, though it is possible that the
  implementation of OEP-26 might build on the infrastructure outlined in this
  document.
* Browser level notifications and event streaming along the lines of
  Server-Sent-Events, GraphQL subscriptions, or frameworks built on top of
  WebSocket. The interactions here are strictly server to server interactions
  between services serving the same site.

Motivation
==========

Open edX has multiple services that need to copy data or take certain actions
when certain events occur in other services. For example:

* The start date of a course exists in both the Catalog domain represented by
  the course-discovery service, as well as in the LMS, where its value is used
  to help determine whether an enrolled student will have access to the
  content.
* Other services often have a user table, with data replicated from the LMS
  (the place where users register today).
* Publishing a new version of course content in Studio triggers a variety of
  LMS tasks to query and re-build optimized versions of course data for various
  systems like the Block Transformers that power the mobile application and
  course outline.
* Grading events in the LMS may trigger certificate generation if the user has
  exceeded the required threshold.

The number of services is steadily growing, and the ad hoc synchronization and
communication methods we've implemented over the years have caused us a great
deal of operational pain, particularly around course discovery. At the same
time, recent discussions around Domain Driven Design and data replication have
given us a better framing for the kind of relationship we want to have between
various services. There has also been a strong desire for a properly documented
set of integration APIs that would allow Open edX to better interoperate with
other applications in the ecosystem.

There is an opportunity to bring this all these elements together and create a
solid foundation for simple, reliable, and near real-time inter-service
communication. But without some planning and design work, we may end up in an
even more chaotic place where each pairing of services uses different
conventions for messaging each other.


Specification
=============



Message Format
~~~~~~~~~~~~~~

The message format will use
`CloudEvents 1.0 <https://github.com/cloudevents/spec/blob/master/spec.md>`_
specification, with additional usage conventions that all Open edX services
should adhere to. CloudEvents was developed by the Cloud Native Computing
Foundation, with participation from major companies like Google, Microsoft, IBM,
Red Hat, Oracle, and Huawei. While the finalized spec is only months old and the
Python tooling is primitive (advertised to break at any time), CloudEvents is
also a pretty minimal message envelope spec, with the basic required fields
being things that we'd have anyway.

Event messages will be JSON documents < 64K. An example with comments:

.. code-block:: javascript

  {
    // Message ID. CloudEvents requires this to be a string that is unique for
    // any given source. We can just use a UUID1 here, as it's simple to
    // generate and there are no requirements around randomness.
    "id": "83f8287e-56c2-11ea-a800-f2189839bdb6",

    // Mandatory, and refers to the version of CloudEvents.
    "specversion": "1.0",

    // CloudEvents only requires that this be a string describing the type of
    // event, but recommends that it be prefixed with a reverse-DNS name for
    // namespacing purposes. In this example, "org.openedx" is the generic
    // prefix that would be used for all Open edX installs. The next component
    // is the Domain, in this case "catalog". The next component is the version,
    // (see versioning), followed by a noun + verb convention. Note that the
    // nouns and verbs don't have to map directly onto CRUD operations. Also,
    // verbs should be past-tense, for consistency with most of the tracking log
    // events we currently emit.
    //
    // The standard ones will be: created/updated/deleted
    "type": "org.openedx.catalog.v1.course.created"

    // Time the message is created, in UTC using RFC3339. This should be
    // separate from any "updated_at" sort of timestamp for a record update
    // event, as the message may be generated long after the update actually
    // happens.
    "time": "2020-02-23T09:00:00Z",

    // TODO: source, dataschema, datacontenttype???

    // This is domain-specific. We should have only the vaguest guidelines here,
    // like to prefer underscored names to camelCase.
    "data": {

    }
  }

Message Content Guidelines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Avoid callbacks.
2. Self-contained if at all possible.
3. Can have pointers to large static resources if necessary?
4. No RPC
5. Exchange = Domain, routing key = event type. Namespacing excessive?

Transport Layer
~~~~~~~~~~~~~~~

Kombu, Redis as default.

Versioning
~~~~~~~~~~~

Testing and Ensuring Compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bootstrapping
~~~~~~~~~~~~~

Documentation
~~~~~~~~~~~~~

(AsyncAPI)






Rationale
=========



Backward Compatibility
======================



Reference Implementation
========================



Rejected Alternatives
=====================



Change History
==============

