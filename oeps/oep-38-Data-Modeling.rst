======================================
OEP-38:  Data Modeling Best Practices
======================================

+---------------+------------------------------------------------------------+
| OEP           | :doc: `OEP-0038 <oep-0038-data-modeling>`                  |
+---------------+------------------------------------------------------------+
| Title         | Data Modeling Best Practices                               |
+---------------+------------------------------------------------------------+
| Last Modified | 2019-08-01                                                 |
+---------------+------------------------------------------------------------+
| Author        | Brian Beggs <bbeggs@edx.org>                               |
+---------------+------------------------------------------------------------+
| Arbiter       | Albemarle                                                  |
+---------------+------------------------------------------------------------+
| Status        | Provisional                                                |
+---------------+------------------------------------------------------------+
| Type          | Best Practices                                             |
+---------------+------------------------------------------------------------+
| Created       | 2019-05-01                                                 |
+---------------+------------------------------------------------------------+
| Review Period | 2019-08-01 - 2019/08/16                                    |
+---------------+------------------------------------------------------------+
| Resolution    | 'Original pull request' m                                  |       
+---------------+------------------------------------------------------------+

Abstract
========

In order to obtain the highest possible value from data collected in the
OpenedX ecosystem, this document attempts to provide guiding thoughts and
principles on data modeling.

These data modeling guidelines are broken down into three sections:

-  Guiding Principles

-  Data Modeling Standards

-  Best Practices

Guiding Principles - This section is intended to give the reader a
framework to think about data modeling at edX. While no specific
instructions are given in this section, it should act as a framework to
help the reader think about data modeling on the whole at edX.

Data modeling standards - The core guidelines that everyone must adhere
to when creating new data models at edX. These standards are designed to
ensure edX can gain the highest value and insights from the data. They
should be applied as the basic standard for all applications in the
OpenedX ecosystem.

Data Best Practices - These practices are designed to help teams create
rich and efficient data models within the OpenedX ecosystem. They are not
standards but guidelines to help teams think about how they are storing
data.

This OEP explicitly does not cover:

-  Data warehousing

-  Data pipelines

-  Event data


Definitions
===========

BI - Business intelligence. Technologies, applications and practices for
the collection, integration, analysis, and presentation of business
information. The purpose of Business Intelligence is to support better
business decision making.

Composite Key - in the context of relational databases, a composite key
is a combination of two or more columns in a table that can be used to
uniquely identify each row in the table. Uniqueness is only guaranteed
when the columns are combined; when taken individually, the columns do
not guarantee uniqueness.

CUD - Create, Update, Delete. These are some of the actions that may
happen to data in a database system.

Data Best Practices - These practices are designed to help teams create
rich and efficient data models within the OpenedX ecosystem. They are not
standards but guidelines to help teams think about how to store data.

Data Dimension - A Data Dimension is a set of data attributes pertaining
to something of interest to a business. Dimensions are things like
"customers", "products", "stores" and "time". For users of Data
Warehouses, data dimensions are entry points to numeric facts (e.g.
sale, profit, revenue) that a business wishes to monitor.

Data Modeling - A process used to define and analyze data requirements
needed to support the business processes within the scope of
corresponding information systems in organizations.

Data Modeling Standards - The most basic and standard design principles
for data modeling. These standards must be adhered to when creating new
models or updating existing models in the OpenedX ecosystem.

IDA - “Independently Deployed Application”. Similar to a microservice or
stand alone application.


OpenedX Ecosystem
------------------

Used in this document, the phrase "OpenedX Ecosystem" includes the
services that comprise and support the running of an OpenedX
installation (e.g.,. edx.org). This includes:

- The LMS and Studio

- IDAs such as Notes, Ecommerce, and Forums

- Backend systems such as data analytics and operational logging

- Third party services used to extend functionality of applications, such as Optimizely, Google Analytics, and Sailthru


Specification
=============

Guiding Principles
------------------

Data is an asset
~~~~~~~~~~~~~~~~

edX uses data to decide which people should receive marketing emails,
who passes or fails a course, or how much to pay our partners. Our
partners use our data to target users who fit certain criteria and to
refine and improve their courses. The data we collect today may be used
to gain new educational insights we have yet to realize. Decision making
at edX should be data-driven and based on this collected data.

This data is one of our most valuable assets and it
should be a first-order concern. Save everything (disk is cheap). The more data we are able to collect about users,
behavior, and system state now, the more opportunities we’ll have to
improve our decision-making in the future.

Use industry best practices
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Data stored in the OpenEdX ecosystem should adhere to industry best
practices. For example, since an industry best practice uses numerical
identifiers to identify rows of data, the OpenedX ecosystem should also use
numerical identifiers. Adhering to industry best practices and the
practices outlined in this document ensures our data is approachable for
experienced engineers and new hires alike and allows us to leverage 3rd
party tools more easily to assist in the analysis phase.

Don’t make me think
~~~~~~~~~~~~~~~~~~~

Steve Krug, the author of “Don’t Make Me Think”, says: “Your objective
should always be to eliminate instructions entirely by making everything
self-explanatory, or as close to it as possible.” This principle should
also be applied to data modeling. Data models should have descriptive
names clearly identifying the data that the model holds. The field names
should make sense to most people familiar with the domain, without
needing much clarifying documentation.

Consider the analytics user
~~~~~~~~~~~~~~~~~~~~~~~~~~~

For every hour of engineer-time spent creating a data model. Many
more are spent using the model and analyzing its data. Taking
the time to consider how the data will be used, and thoughtfully design
our data representation, can potentially save many hours during the
analysis phase.

This is especially true since changing data models, once they are in
use, can be time-consuming. A data model change in a core application
can affect many other systems. Small changes to a data model could
possibly cause hours or days of work for different teams throughout the
organization:

-  Data engineering and Analytics to update their workflows

-  BI teams to update reports that utilize this data,

-  Devops to deploy migrations to large tables, with the risk of
      potential downtime.

Due to the potentially high cost of changing a model, it pays to get it
as correct as possible the first time.



Think about the person who will analyze this data later and the person
who manages the system day to day. (That person could possibly be future
you!) Try to do as much careful design up front to make your quality of
life better later.

History is best (but events are a close second)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Historical data accuracy is best when history about a change is captured at the database level. Changes in data relating to financial systems (e.g. enrollments, payments, course price changes) should be stored historically where the change is made, preferably in the same system as the system of record. This allows us to reconstruct the data at different points with much greater reliability.

If, for some reason, it is not possible to create history at the time of
entry (for example, if we expect the table to become too large or the
writes to be too frequent), it is recommended that an event be issued in its
place.


Data Modeling Standards
-----------------------

All models in the OpenedX ecosystem should have:

-  A Primary Key

   -  Should be named: “id”

   -  Use numeric based identifiers

   -  A key size large enough so the keys will not run out for a very
         long time.

   -  Do not use composite based primary keys. Use a primary key column

-  Updated and created timestamps

   -  Use UTC time.

   -  The preferred method for doing this in OpenedX Django applications is to inherit the "TimeStampedModel" class.

   -  If for some reason you can not inherit from "TimeStampedModel use the following naming conventions:

      -  Created date should be named: “created”

      -  Updated date should be named: “modified”

-  Use a numeric foreign key(s)

   -  Data should be joined using primary keys wherever possible

   -  When designing 

   -  Do not join on things such as username, email address, or other
         dimensions of data that may change over time

   -  Do not join on PII

-  The minimum number of indexes possible to make the table/queries
      performant

   -  Indexes cost space and have their own set of performance concerns.

   -  Over-indexing data could actually make the database less
         performant (slower writes/updates)

-  History for models involved with enrollments, courses and course metadata, or data involving financial payments and transactions.

-  History using django-simple-history_. .. _django-simple-history: https://django-simple-history.readthedocs.io/en/latest/

   -  Should capture the ID of the user who made the change

-  Where Django simple history is not an option, the following data
      should be captured:

   -  Fields that were changed

   -  Date & time of the change

   -  ID of the user who initiated the change

-  The correct data type for a column.

   -  Don’t use a long when an Int or bool would do.

   -  Use Integers for your keys

   -  Don’t store an int as varchar

-  Don’t mix types in a column

-  Annotations

   -  All models in the OpenedX ecosystem should be tagged for PII
      using `code
      annotations <https://github.com/edx/code-annotations>`__\  following \ `OEP-30 <https://github.com/edx/open-edx-proposals/blob/master/oeps/oep-0030-arch-pii-markup-and-auditing.rst>`__

Best Practices 
~~~~~~~~~~~~~~~

Deleting data:

-  It is better to have a column to mark the record as inactive than to
      remove the data from the system using the SQL delete keyword.

   -  Please note that GDPR may require the data be deleted. If a field
         is determined to contain PII and falls under the realm of GDPR,
         That data should be deleted from the system.

Don’t trap the data

-  Each piece of information should have its own column. Avoid storing
      data in blob fields or as JSON in the database.

-  Don’t store encoded (pickle, json, other) objects in the database! If
      you need to run the python environment to decode the data, the
      data is worthless.

Store everything

-  Storage is cheap!

-  If you are unsure whether you should store something in the database,
      add history, or fire an event, the answer is almost always yes

-  Still not sure? The default answer is yes.

Views should access models via methods on those models, instead of
querying managers directly.

-  For example, prefer creating something like
      CourseEnrollment.is_enrolled(...) rather than having views check
      CourseEnrollment.objects.filter(...).exists().

-  This allows us to more easily make internal representation changes
      like switching to using a “deleted” flag instead of deleting the
      row.

-  This also reduces the likelihood that people will query models in a
      non-performant way (e.g. sorting by an unindexed field).

Enforce logical constraints at the database layer.

-  If your code expects a 1:1 relationship, make that a unique
      constraint in the database instead of trying to just enforce it in
      Python.

-  Python will not save you from race conditions. Database constraints
      will.

-  For example, an enrollment should have a unique constraint on
      (course_id, user_id), since a given user should only have one
      enrollment per course.

-  This is a smaller version of “don’t allow impossible states to be
      represented in the database”.

Developer Responsibility

------------------------

It is the responsibility of the developer to adhere to all of the
standards in the Data Modeling Standards section of this document.

Code Reviewer Responsibility

----------------------------

The code reviewer is responsible for ensuring the standards set forth in
the Data Modeling Standards section of this document are met.

Responsibility for Third-party Service Integrations

---------------------------------------------------

Not sure… Adhere to the same standards.

Backward Compatibility

======================

Data models that are not within the standards of this document should be
updated the next time a team needs to make changes to that model

Some things are exempt from this process:

-  Do not update primary key types without business/technical
      justification, as there may be many downstream changes that will
      need to change as well.




                 
