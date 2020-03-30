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

Open edX has multiple services that need to copy data or take specific actions
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

Open edX services already use `Django signals
<https://docs.djangoproject.com/en/1.11/topics/signals/>`_ for subscribing to
events within the same service, but we have no accepted recommendation for how
to do something similar across service boundaries. As the number of services in
Open edX has grown, the various ad hoc synchronization and communication methods
we've implemented over the years have caused numerous operational and
performance problems (particularly around catalog data). At the same time,
recent discussions around Domain Driven Design and data replication have given
us a better framing for the kind of relationship we want to have between various
services. There has also been a strong desire for a properly documented set of
integration APIs that would allow Open edX to better interoperate with other
applications in the ecosystem.

There is an opportunity to bring all these elements together and create a solid
foundation for simple, reliable, well documented, near real-time cross-service
message passing. But without some planning and design work, we may end up in an
even more chaotic place where each pairing of services uses different
conventions for this kind of communication.


Specification
=============

Message Format
~~~~~~~~~~~~~~

The message format will use `CloudEvents 1.0
<https://github.com/cloudevents/spec/blob/master/spec.md>`_ specification, with
additional usage conventions that all Open edX services should adhere to.
CloudEvents was developed by the Cloud Native Computing Foundation, with
participation from major companies like Google, Microsoft, IBM, Red Hat, Oracle,
and Huawei. While the finalized 1.0 spec is only months old and the Python
tooling is primitive (advertised to break at any time), CloudEvents is also a
pretty minimal message envelope spec, with the basic required fields being
things that we'd have anyway.

Event messages will be JSON documents < 64K. An example with comments:

.. code-block:: javascript

  {
    // Message ID. CloudEvents requires this to be a string that is unique for
    // any given source. We can just use a UUID1 here, as it's simple to
    // generate and there are no requirements around randomness.
    "id": "83f8287e-56c2-11ea-a800-f2189839bdb6",

    // This should be the originating service, or some combination of
    "source": "course-discovery/"

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

Message Content Data Guidelines
~~~~~~~~~~~~~~~~~~~~~~~~~~~


Avoid Callbacks
---------------

A callback is when you create a message with an ID or URL that you expect the
consumer to make a synchronous call to when it receives the message. This is
commonly used when an event represents some change that is too large to
practically fit into the 64K message. For instance, we currently emit a generic
"course_published" Django signal whenever data is published in Studio, leading
to a cascade of calls from various apps to the ModuleStore in order to extract
the content data that they need.

Callbacks threaten performance and stability because they reduce a service's
ability to control its own load. For instance, a sudden increase in Courseware
traffic might generate a burst of student analytics events. If this stream of
events overwhelms my service's ability to consume them, the queue may start to
back up with unread events. Yet this shouldn't cause my service to fail, since
it still gets to control how quicky it consumes events off of that queue. It has
the freedom to either slowly catch up (if the burst was a momentary spike), or
to scale up additional workers to handle the higher throughput. My service's
decision to scale up or down does not directly impact other services.

Things change when we introduce a callback to this same scenario. Say the
analytics events now include a callback URL to get basic user information. In
this scenario, doubling the consumers that my service has now also doubles the
load that my service is placing on the REST endpoint serving this user
information. Is that safe? Who knows?

One thing to consider is that whether we can emit multiple events that better
target specific consumer use cases. Let's take the "course_published" event as
an example. Some listeners only care about schedule changes, because they have
to account for when a course starts and ends. Search indexing really only wants
to know the exact bit of content that was modified so that it can update that
text. There is no rule that says a single user action has to translate into a
single event. Be mindful of what your consumers actually care about and the
broad use cases you're trying to serve.

If a callback is still necessary, try to make sure that it points to an
especially robust and performant endpoint. For instance, an event that is fired
when a user changes their profile information might include a URL to the S3
location of their new profile picture. Just keep in mind that messages may be
read long after they're generated, and any presigned S3 URLs you generate might
be expired by the time a consumer gets them.









1. Self-contained, without history
2. PII
4. No RPC
5. Exchange = Domain, routing key = event type. Namespacing excessive?
6. Shouldn't emit before data is committed to the database. on_commit practice?
   (both to prevent mismatch on error as well as to make sure something else
   that might have to read can actually do so without race).

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

