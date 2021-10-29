Don't use openedx-events Attrs Classes to Represent Deserialized Event Bus Objects
==================================================================================

Context
-------

The event bus client libraries have their own object representation of messages. In pulsar, they are defined as `Record`
objects. These objects map easily to Avro Schema definitions and can be used to define the schema. The attrs objects
that we currently have for signals based events don't easily inter-operate with event bus client objects.

Decision
--------

We will create a new utility that can generate Avro schema based on ``attrs`` based objects.

Implementation Notes
--------------------

`PR #30`_ on ``openedx-events`` shows a potential approach to this usig the `built-in metadata`_ that the ``attrs`` library provides for extending ``attrs``.

.. _PR #30: https://github.com/eduNEXT/openedx-events/pull/30
.. _built-in metadata: https://www.attrs.org/en/stable/extending.html

Consequences
------------

* There will be bridging code that will abstract away schema generation from most developers of events.  This may have a negative impact as it might make it harder for developers to reason about schema evolution.

* For non primitive types(eg. Opaque Keys objects), the bridging code between ``Avro`` and ``attrs`` will need to have special serializers or clearly fail.
