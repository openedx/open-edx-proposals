####################################
OEP-46: Domain-Driven-Design Domains
####################################

.. list-table::
  :widths: 25 75

  - - OEP
    - :doc:`OEP-46 </oeps/oep-0046-arch-ddd-domains.rst>`
  - - Title
    - Domain-Driven-Design (DDD) Domains
  - - Last Modified
    - 2020-04-28
  - - Authors
    - Cale Pennington <cale@edx.org>
  - - Arbiter
    - <Arbiter's real name and email address>
  - - Status
    - Draft
  - - Type
    - Architecture
  - - Created
    - 2020-04-23
  - - Review Period
    - 2020-04-23 - 2020-05-30
  - - Resolution
    - 
  - - References
    - 

Abstract
********

Open edX is a large software project with a number of different teams working
various portions of it. Using Domain Driven Designs (and in particular, Bounded
Contexts) to enumerate the distinct portions of the system and how they can
interact will allow teams to work with minimal coordination.

Motivation
**********

Domain Driven Design presents a description of the Open edX system that can
be used to clarify decisions in the following areas:

  - Communication
    - Should these two components communicate synchronously or asynchronously?
    - Is this API using the same terminology that my component is?
  - Level of effort
    - Should we consider buying this component rather than custom building it?
    - Is this component something an Open edX operator might want to replace?

Use Cases
=========

In order to clarify the Subdomains used by Open edX, the following are usecases identified
as being important to edX, and to the Open edX project.

Educators
---------
- Authoring learning components to later be delivered to a learner.
- Rich eco-system of learning components.

Motivation:
  - I want to teach millions of learners around the world, and know my time is a good investment. 
  - I want to improve as an educator.
  - I want to elevate my brand and attract millions and millions.
  - I want to connect with and learn from a community of educators.
  - I want to make money.
  - I want to do academic research and publish some papers. 
  - I want to effectively, and easily do my job (course build, course run, learner management, data review).
    - Effectively includes ability to pick and choose learning components.

Use Cases:
  **Educator.1** Easy Authoring  *(Core)*:

    - Extensible educational blocks
    - Course Design (Structure - outline, etc)
    - Educational Blocks (Teaching components and Assessment)


  **Educator.2** Administration of Courses *(Core)*:

    - Launch a course run.
    - Collect revenue share.

  **Educator.3** Actionable Data and Feedback *(Supporting)*

    - What are my students doing

  **Educator.4** Communities of Practice *(Supporting or Generic?)*

  **Educator.5** Supporting Learners (Community and Mentorship) *(Supporting or Generic?)*

Learners
--------

  - Consume content to achieve learning outcomes.
  - Determining learning pathway.
    - Inside and outside of course.

Motivation:
  - I want to learn, so that I can improve my life (career change, improve my job, raise, change roles, fulfill educational requirements).
  - I am a lifelong learner.
  - I want to connect with others in similar fields of interest.

Use Cases:
  **Learner.1** Discovery: Find the right course/program to help them achieve their goals *(Supporting or Generic?)*

  **Learner.2** Achieve Learning Outcomes *(Core)*:

    - Rigor/quality of instruction

  **Learner.3** Support through their learning pathway – progress, messaging, social *(Supporting or Generic?)*

  **Learner.4** Proof of Learning, credentials/certifications *(Supporting or Generic?)*

Admins
------

Motivations:
  Enterprise Admins:
    - I want to effectively, and easily do my job (course curation, employee management, data review).
    - I want to know my time and my employee's time is a good investment.
    - I want to upskill my employees.
    - I want to validate completion of internal requirements (legal / business policies).
    - I want to retain my employees (a benefit).
    - I want to create a pathway for my employees.
  Educator Admins:
    - I want to effectively, and easily do my job (learner management, data review).

Use Cases:
  **Admin.1** Actionable Data and Feedback *(Supporting)*:

    - Am I getting my money's worth

  **Admin.2** Setting up edX for their org *(Supporting)*:

    - Curation
    - Integration

Specification
*************

The Open edX system consists of the following bounded contexts.

Terminology and Behaviors
=========================
Subdomain:
  A group of related bounded contexts. A Subdomain is **Core** if it is a key differentiator for
  Open edX, **Supporting** if it has Open edX specifics, but isn't **Core**, or **Generic** if
  it has no Open edX business context.

  There should be **no** synchronous communication between separate subdomains.

Bounded Context:
  A single service inside a Subdomain. Each Bounded Context owns its own data storage,
  has its own Ubiquitous Language that describes all of the entities owned by that service,
  and uses Anti-Corruption Layers to translate incoming objects into that language.

  Bounded Contexts within a Subdomain **should** use asynchronous communication with
  other Bounded Contexts in the same Subdomain, but may elect to use synchronous
  communication if required.

  Like Subdomains, **core** Bounded Contexts in a Subdomain implement the
  key Open edX business logic for that Subdomain. **supporting** Contexts
  have Open edX logic, but are not **core** to their Subdomain, and **generic**
  contexts should be replaceable and contain no Open edX business logic.

Ubiquitous Language:
  The set of terms and definitions that defines the entities and relationships
  within a Bounded Context. These terms may not have the same definitions across
  different Bounded Contexts.

Anti-Corruption Layer:
  A layer around the edge of a Bounded Context that converts entities and information
  retrieved from other Bounded Contexts in the same Subdomain into the Ubiquitous Language
  of this Bounded Context.

Overall Behavior Flows
======================

A **learner** engages with a **Business Subdomain** to find learning content that meets their
learning goals. They purchase that learning content via whatever mode is appropriate
for that **Business Subdomain** (direct purchase, free enrollment, subscriptions, etc).
The **Business Subdomain** requests fulfillment from the **Learner Subdomain** to allow the
**learner** access to their selected learning content.

An **educator** engages with the **Content Authoring** Subdomain to author learning content
and make it available to the **Learning** subdomain.

An **administrator** engages with the **Learning** Subdomain to set up learning content with
specific dates, and with the **Business Subdomain** to group learning content
created by **educators** in the **Content Authoring** Subdomain into units that match
learners educational goals.

Learning Content Subdomains
===========================

These subdomains are used to create and consume learning content

Content Authoring
-----------------
**Core** Subdomain

Content Authoring (core) allows educators to create, modify, discover, package, annotate (tag), and share learning content.  Learning content may be packaged from multiple content sources.

Users:
  - Course teams
  - Instructional designers

Use Cases:
  - **Educator.1** Easy Authoring

Bounded Contexts
~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 10 70

   - - Bounded Context
     - Type
     - Description
   - - Catalog Content Authoring
     - generic
     - Catalog Content Authoring (generic) allows educators to create higher-level learning contexts, such as Courses and Programs, which includes metadata related to those contexts.
   - - Course Run Authoring
     -	supporting
     - Course Run Authoring (supporting) allows educators to launch a Course Run. This includes the ability to override configuration settings and fields, including dates and grading policy.
   - - Learning Content Authoring
     - core
     - Learning Content Authoring (core) allows educators to create and publish learning content and pathways,  which can be utilized to create course runs.
   - - Learning Content Repository
     - supporting
     - Learning Content Repository (supporting) provides a shared service for storing, versioning, sharing, and discovering published learning content.  The learning content is available at varying degrees of granularity - including Pathways, Blocks and Units.
   - - Video Processing
     - generic
     - Video Processing  generic) is a service for processing, encoding, and translating authored videos for consumption by learners.

Learning
--------
**Core** Subdomain

Learning allows learners to consume content and perform actions in a learning activity (participating on forums, etc) on the platform.  It also allows educators to support learners via interactions with learners and other support tools.

Users: 
  - Learners
  - Admins

Use Cases:
  - **Learner.2** Achieve Learning Outcomes
  - **Learner.3** Support through their learning pathway – progress, messaging, social
  - **Educator.5** Supporting Learners (Community and Mentorship)

Bounded Contexts
~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 10 70

   - - Bounded Context
     - Type
     - Description
   - - Consuming Learning Content
     - core
     - Consuming Learning Content (core) is the primary context where learners engage with content in personalized learning pathways.  This includes functionality such as:

        - Completion / Progress
        - Digital Journal
        - Grades
        - xBlocks (each as its own separate micro-context): Video, CAPA, ORA, etc.

   - - Social Learning
     - generic
     - Social Learning (generic) provides a service for collaboration and interactivity between learners and educators.

   - - Support Learning
     - supporting
     - Support Learning (supporting) allows educators to administer and manage learners and learning contexts (courses, programs, etc).

   - - Learning Credentials
     - supporting
     - Learning Credentials (supporting) allows learners and educators to access and manage credentials/certifications achieved from learning.

Analytics
---------
**Supporting** Subdomain

Analytics (supporting) allows both educators and admins to access data about learner behavior and proficiencies and content usage in order to assess learners and the pedagogical impact of the content.

Users:
  - Course teams
  - Course Admins

Use Cases:
  - **Educator.3** Actionable Data and Feedback
  - **Admin.1** Actionable Data and Feedback

Users
-----
**Generic** Subdomain

Users allows users to manage their personal profile and demographic inforamtion, and
provides authentication services to other Subdomains.


Business Subdomains
===================

These subdomains are focused on various ways of allowing Learners to find and purchase
learning content experiences.


B2C (Business to Consumer)
--------------------------
**Supporting** Subdomain

B2C (supporting) allows learners to find the right content at the right time to help achieve their learning goals.

Users:
  - Learners
  - Course Admins
  - Marketing

Use Cases:
  **Learner.1** Discovery: Find the right course/program to help them achieve their goals


Bounded Contexts
~~~~~~~~~~~~~~~~

.. list-table::
  :widths: 10 10 70

  - - Bounded Context
    - Type
    - Description
  - - Marketing
    - generic
    - Marketing (generic) provides a portal for learners to discover higher-level learning contexts, such as Courses and Programs.
  - - Catalog Content
    - supporting
    - Catalog Content (supporting) is a service for storing catalog data with interfaces for searching and discovery.  
  - - Catalog Content Authoring
    - generic
    - Catalog Content Authoring (generic) allows educators to author and manage their catalog content.
  - - Purchasing (ecommerce)
    - generic
    - Purchasing (ecommerce) (generic) allows learners to pay for edX content and features.
  - - Content Access / Enrollments
    - supporting
    - Content Access / Enrollments (supporting) is a service for managing access and enrollment information to catalog content.

Enterprise
----------

Users:
  - Enterprise Learners
  - Enterprise Admins

Use Cases:
  - **Admin.1** Actionable Data and Feedback

Bounded Contexts
~~~~~~~~~~~~~~~~

.. list-table::
  :widths: 10 10 70

  - - Bounded Context
    - Type
    - Description
  - - Enterprise Admin
    - supporting
    - TBD
  - - Enterprise Catalog
    - supporting
    - TBD
  - - Enterprise Enrollments
    - supporting
    - TBD


Masters
-------
  **Supporting** Subdomain

  Masters allows educators and learners to manage and engage in bundled packages (programs) of learning
  linked to institutional credit.

Users:
  - Masters Learners
  - Masters Admins
  - Credit Organizations

Use Cases:
  - "Learner.4 Proof of Learning, credentials/certifications"
  - "Educator.2 Administration of Courses"


Bounded Contexts
~~~~~~~~~~~~~~~~

.. list-table::
  :widths: 10 10 70

  - - Bounded Context
    - Type
    - Description
  - - Program Enrollments
    - core
    - Program Enrollments enable educators to handle grading, completion, and other for learner-facing features within the context of a program.
  - - Registrar
    - supporting
    - Registrar allows educators to administer programs, using identifiers from external systems, through the Program Manager interface.
  - - Program Credentials
    - generic
    - Program Credentials allows learners and educators to access and manage credentials and certifications achieved from engaging in Programs.


Rationale
*********

The rationale adds to the specification by describing the events or
requirements that led to the proposal, what influenced the design, and why
particular design decisions were made. The rationale could provide evidence
of consensus within the community and discuss important objections or
concerns raised during discussion. It could identify any related work, 
for example, how the feature is supported in other systems.

Backward Compatibility
**********************

This statement identifies whether the proposed change is backward compatible.
An OEP that introduces backward incompatibilities must describe the
incompatibilities, with their severity and an explanation of how you propose to
address these incompatibilities.

Reference Implementation
************************

The reference implementation must be completed before any OEP is given "Final"
status, but it need not be completed before the OEP is "Accepted". While there is
merit to the approach of reaching consensus on the specification and rationale
before writing code, the principle of "rough consensus and running code" is
still useful when it comes to resolving many discussions.

Rejected Alternatives
*********************

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

Change History
**************

A list of dated sections that describes a brief summary of each revision of the
OEP.
