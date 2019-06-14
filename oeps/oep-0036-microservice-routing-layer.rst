===================================
OEP-36: Microservice Routing Layer
===================================

.. list-table::

  * - OEP
    - :doc:`oep-0036-microservice-routing-layer`
  * - Title
    - Microservice Routing Layer
  * - Last-Modified
    - 2019-04-25
  * - Author
    - Fred Smith <derf@edx.org>
  * - Arbiter
    - David Joy <djoy@edx.org>
  * - Status
    - Draft
  * - Type
    - Architecture
  * - Created
    - 2019-04-25

Abstract
========

Proposes a key Open edX component that will connect users to applications using a layer 7 (application layer/URI Based) routing framework. 

Motivation
==========

Microservices are currently implemented using layer 4 (DNS) based routing. Each microservice occupies a unique hostname, with dedicated ELB or CDN infrastructure to support routing user traffic to the service. This pattern is simple to monitor, deploy, and manage, but slow and expensive to build, and regularly causes problems with cross service user information sharing. In addition, modifying or splitting a monolithic service into microservices results in confusing URI changes and costly migrations.

Specification
=============

A layer 7 routing application will be chosen that meets the following requirements:

* High performance connection management capabilities, including support for slow clients or backends, and wire-speed throughput.
* SSL termination on the router.
* Flexible configuration backends including container service discovery, flat files, and filesystem routing.
* Path based routing, including path subsets and regular expression matching

This layer 7 routing application will be deployed on a common hostname.

Rationale
=========

A layer 7 routing layer would allow us to separate the user facing URI or API endpoint from the backend implementation. It would allow microservices to exist that only manage a small number of endpoints, without requiring developers to build out load balancing or CDN infrastructure. 

This change will allow developers, Open edX operators and resellers to deploy new Open edX sites quicker and easier than they currently can.  The router will provide a map for people to understand which services they need to deploy.


Service layers of concern
======

Routing Layer
----
* Web Application Firewall
* Connection queuing
* Service routing
* SSL termination

Static file hosting layer
---
* asset serving

Application Layer 
---
* JWT validation
* Rate limiting
* access control


Backwards Compatibility
=======================

Services will begin to move API and user endpoints behind this layer 7 router after declaring their top level routing path. eg.

* the LMS would occupy /courses
* the marketing site would occupy /course, /school, /micromasters
* ecommerce would occupy /orders, /payments
* studio would occupy /studio 

links between services would begin to point to the new endpoints, and old endpoints serve 301 redirects.

Old URIs will be migrated using 301 redirects, which will automatically migrate users browsers to the new endpoints. For an overlapping time period, both URIs will be maintained and function, so that services can transition to the new schema.

Definitions
=====

Microservice
---

A microservice is a web application that supports a specific task or business goal.  For the purposes of this proposal, this term encompasses django applications, IDAs, Microfrontends, rails applications, and any other service that supports a specific task or business goal in the Open edX stack.

Layer 7
---

In the OSI model, Layer 7 is the Application layer.  For the purposes of this proposal, we are using this term to describe the URI Path requested in an HTTP request.


Change History
==============

2019-04-30 - Proposed,  Fred Smith <derf@edx.org>



