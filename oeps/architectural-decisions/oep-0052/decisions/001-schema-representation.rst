Schema Representation
==================================================================================

Context
-------

* It is a best practice to use an explicit schema definition. Avro is the recommended serialization format for Kafka.

* The attrs objects that we currently have for signal-based events don't easily inter-operate with event bus client objects. Source ADR: `0003-events-payload.rst`_

.. _0003-events-payload.rst: https://github.com/eduNEXT/openedx-events/blob/1a65c11d2a126bc2e651eaf259df20b8427a5bd2/docs/decisions/0003-events-payload.rst


* Industry best practices seem to suggest using a binary encoding of messages.

* Industry best practice also suggests using AVRO over JSONSchema for better expression around schema evolution.

* Even if we use a JSON encoding for the message we'll probably want to enable compression on the wire to reduce data
  size.  This would mean whether or not we use JSON encoding is moot because the data on the wire will still be binary
  encoded.

Decision
--------

* We will continue using ``attrs`` decorated classes for explicit schema definition.

* We will also use the binary serialization of messages that are transmitted over the event bus.

* The binary encoding of messages will use AVRO specification.

* A new utility will be created to auto generate Avro schema based on ``attrs`` decorated classes.

Implementation Notes
--------------------

`AvroAttrsBridge`_ class is a potential approach to this using the `built-in metadata`_ that the ``attrs`` library provides for extending ``attrs``.

.. _AvroAttrsBridge: https://github.com/eduNEXT/openedx-events/blob/1a65c11d2a126bc2e651eaf259df20b8427a5bd2/openedx_events/avro_attrs_bridge.py#L17
.. _built-in metadata: https://www.attrs.org/en/stable/extending.html

Consequences
------------

* There will be bridging code that will abstract away schema generation from most developers of events.  This may have a negative impact as it might make it harder for developers to reason about schema evolution.

* For non primitive types (eg. Opaque Keys objects), the bridging code between ``Avro`` and ``attrs`` will need to have special serializers or clearly fail.

* Any reference to using JSON or JSONSchema in :ref:`OEP-41 <oep_41>` should be review and updated to clarify implied or explicit decisions that
  may be reversed by this decision.
