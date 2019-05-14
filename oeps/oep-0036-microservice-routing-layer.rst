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
    - Provisional
  * - Type
    - Architecture
  * - Created
    - 2019-04-25

Abstract
========

Proposes a system with which users can interact with microservice applications
using a layer 7 routing framework. 

Motivation
==========

Microservices are currently implemented using layer 4 (DNS) based routing. Each
microservice occupies a unique hostname, with dedicated ELB or CDN 
infrastructure to support routing user traffic to the service. This pattern
is simple to monitor, deploy, and manage, but slow and expensive to build,
and regularly causes problems with cross service user information sharing. In
Addition, modifying or splitting a monolithic service into microservices resuls
in confusing URL changes and costly migrations.

A layer 7 routing layer would allow us to separate the user facing URL or API
endpoint from the backend implementation. It would allow microservices to exist
that only manage a small number of endpoints, without requring developers to 
build out load balancing or CDN infrastructure. 

We are currently using a layer 7 routing framework for prospectus, allowing
us to replace drupal generated pages with gatsby generated pages quickly and
seamlessly.

This change will allow developers, openedx operators and resellers to deploy
new openedx sites quicker and easier than they currently can.  The router
will provide a map for people to understand which services they need to deploy.


Specification
=============

A layer 7 routing application will be chosen that meets the following
requirements:

* High performance connection management capabilities, including support
  for slow clients or backends, and wire-speed throughput.
* SSL termination on the router.
* Flexible configuration backends including container service discovery,
  flat files, and filesystem routing.
* Path based routing, including path subsets and regular expression matching

This layer 7 routing application will be deployed on a common hostname.

Services will begin to move API and user endpoints behind this layer 7 router
after declaring their top level routing path. eg.

* the LMS would occupy /courses
* prospectus would occupy /course, /school, /micromasters
* ecommerce would occupy /basket
* studio would occupy /studio 

links between services would begin to point to the new endpoints, and old endpoints
serve 301 redirects.

Possible additional functionality that may be implemented at the l7 router:

* Authentication
* Rate limiting
* Web Application Firewall


Backwards Compatibility
=======================

Old URLs will be migrated using 301 redirects, which will automatically migrate
users browsers to the new endpoints. For an overlapping time period, both
URLs will be maintained and function, so that services can transition to the
new schema.

Change History
==============

2019-04-30 - Proposed,  Fred Smith <derf@edx.org>
