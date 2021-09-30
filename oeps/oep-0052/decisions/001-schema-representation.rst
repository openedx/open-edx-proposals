Don't use openedx-events Attrs Classes to Represent Deserialized Event Bus Objects
==================================================================================

Context
-------

The event bus client libraries have their own object representation of messages. In pulsar, they are defined as `Record`
objects. These objects map easily to Avro Schema definitions and can be used to define the schema. The attrs objects
that we currently have for signals based events don't easily inter-operate with event bus client objects.

Decision
--------

We will define and manage event bus schema definition and related object definition independently of the signals based
attrs objects.

Consequences
------------

We will have potentially different schema for what data is available within a service compared to between services.

Rejected Alternatives
---------------------

Make new schema classes that can generate schema based on the `attrs` classes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It should be possible to use the introspection information that attrs provides for
[extension](https://www.attrs.org/en/stable/extending.html) to be able to generate schema based on attrs classes. This
along with some guidelines could allow us to use the attrs classes as the de-facto definition of schema for the Open edX
Platform. We are currently rejecting this approach because of the level of effort involved. We don't want to wait for
this to exist before we can benefit from using the event bus. In the future we may still want to build this.
