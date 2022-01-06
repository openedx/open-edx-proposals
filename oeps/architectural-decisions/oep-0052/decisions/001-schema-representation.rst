Schema Representation
==================================================================================

Context
-------

The event bus client libraries have their own object representation of messages. In pulsar, they are defined as `Record`
objects. These objects map easily to Avro Schema definitions and can be used to define the schema. Also, Avro has been the default Kafka serialisation mechanism for a while.

The attrs objects that we currently have for signals based events don't easily inter-operate with event bus client objects.

Decision
--------

We will create a new utility that can generate Avro schema based on ``attrs`` based objects.

Implementation Notes
--------------------

`AvroAttrsBridge`_ class is a potential approach to this using the `built-in metadata`_ that the ``attrs`` library provides for extending ``attrs``.

.. _AvroAttrsBridge: https://github.com/eduNEXT/openedx-events/blob/1a65c11d2a126bc2e651eaf259df20b8427a5bd2/openedx_events/avro_attrs_bridge.py#L17
.. _built-in metadata: https://www.attrs.org/en/stable/extending.html

Consequences
------------

* There will be bridging code that will abstract away schema generation from most developers of events.  This may have a negative impact as it might make it harder for developers to reason about schema evolution.

* For non primitive types(eg. Opaque Keys objects), the bridging code between ``Avro`` and ``attrs`` will need to have special serializers or clearly fail.
