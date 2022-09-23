Remote Dev Data Generation
##########################

Status
******

Accepted

Context
*******

Devstack currently has no standard way of creating dev data. Over time, developers have figured out their own ways to create data for local development. Recently, `OEP-37`_ was proposed, which suggests a standardized method for creating dev data. We decided the proposal was good enough to warrant a prototype and during the prototyping phase, we ran into situations where related data was necessary across multiple IDAs.

During provisioning as it is currently done, an IDA that needs specific data in another IDA will do an API call to create that data. One example is when ecommerce publishes course data to LMS to ensure that LMS data match ecommerce data.

Decision
********

We do not want additional coupling between IDAs, so we have opted not to do remote data generation in the implementation of `OEP-37`_. Specifically, developers will need to fully specify what data they want to create in each IDA and they should not assume creating some data in one IDA's database will result in data being created in another.


Consequences
************

- No remote calls should be made from within dev data generation functions.
- More specific data will need to be specified in YAML files
- There will be more coupling between different YAML files


.. _OEP-37: https://github.com/openedx/open-edx-proposals/pull/118
