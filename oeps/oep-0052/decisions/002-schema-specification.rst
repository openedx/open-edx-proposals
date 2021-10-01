Event Bus Messages will be Defined in AVRO Specification Language
=================================================================

Context
-------

* Currently, Pulsar does not support `JSON Schema <https://json-schema.org/specification.html>`__, it only supports AVRO
  for schema definition. Pulsar does support serializing AVRO to JSON (confusing using a ``JSONSchema`` class).

* Industry best practices seem to suggest using a binary encoding of messages.(TODO, links)

* Industry best practice also suggests using AVRO over JSONSchema for better expression around schema evolution. (TODO,
  links)

* Even if we use a JSON encoding for the message we'll probably want to enable compression on the wire to reduce data
  size.  This would mean whether or not we use JSON encoding is moot because the data on the wire will still be binary
  encoded.

Decision
--------

* We will use the AVRO specification to define the schema of messages that are sent over the event bus.

* We will also use the binary serialization of messages that are transmitted over the event bus.

Consequences
------------

* Any reference to using JSON or JSONSchema in :ref:`OEP-41 <oep_41>` should be review and updated to clarify implied or explicit decisions that
  may be reversed by this decision.
