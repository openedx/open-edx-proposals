#################################
OEP-0018: Developer Documentation
#################################

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-0018 </oeps/oep-0018>`                       |
+-----------------+--------------------------------------------------------+
| Title           | Developer Documentation                                |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2018-03-27                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Nimisha Asthagiri                                      |
|                 | Grant Goodman                                          |
+-----------------+--------------------------------------------------------+
| Arbiter         | Ed Zarecor ezarecor@edx.org                            |
+-----------------+--------------------------------------------------------+
| Status          | Draft                                                  |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2018-03-27                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | <start - target end dates for review>                  |
+-----------------+--------------------------------------------------------+
| `Resolution`    | <links to any discussions where the final              |
|                 | status was decided>                                    |
+-----------------+--------------------------------------------------------+

********
Abstract
********

Developers who work on the Open edX platform, both inside and outside of edX,
need accurate and current documentation about platform architecture, APIs, and
development best practices.

**********
Motivation
**********

Developer documentation has been inconsistently and in some cases poorly
maintained. Documents exist on ReadTheDocs (RTD), in repositories on GitHub,
in Confluence, and in Google Docs.

*************
Specification
*************

We should distinguish between documentation that is strictly internal to edX
operations and documentation that is helpful for any Open edX platform
develoers. Documentation for the Open edX platform should be maintained in the
relevant Git repository, in RST format.

==========================================
Index of Developer Documentation Resources
==========================================

Create a document that indexes all available documentation resources for
developers. This will help users discover what's available and also help edX
maintain and control the resources. Much of the information that would be
included is on the Confluence
`Developer Documentation<https://openedx.atlassian.net/wiki/spaces/OpenDev/pages/159881063/Developer+Documentation>`
 page.

======================================
Developer Best Practices Documentation
======================================

Some developer best practices documentation is maintained in a RTD
documentation titled
:ref:`Open edX Developer’s Guide<http://edx.readthedocs.io/projects/edx-developer-guide/en/latest/>`.
We need to determine the best location for this content and locate other
related topics currently in Confluence that we should add to this
documentation.

==============
API references
==============

API reference documentation should be generated from code and maintained in the
related repository. We need to determine the best method for generating and
publishing API reference docs in the near future. At this point, it seems
likely that using Swagger and publishing the API reference docs within the
related repository will be the best approach.

*********
Rationale
*********

Developer documentation should aim to meet the following requirements.

   * **Discoverable**. Users must be able to find the documentation that is
     relevant to their needs.
   * **Maintainable**. Writers, editors, and reviewers should be able to
     create and modify documentation without too much effort. Anyone, inside
     or outside of edX, should have the ability to contribute to documentation.
   * **Version Controlled**. Documentation should be maintained under version
     control, which also promotes orderly review of documentation.

**********************
Backward Compatibility
**********************

We will need to migrate content from deprecated sources such as Confluence. As
part of the migration, we will need to establish a timetable for deleting
migrated content and determine a method for redirecting users to the new
location of the content.

We will need to provide resources and other support so that everyone who needs
to write documentation using RST is comfortable doing so. We have an existing
document, the :ref:`edX Style Guide<http://draft-edx-style-
guide.readthedocs.io/en/latest/index.html>`, which includes a complete
reference to using RST in the context of documentation produced by the Docs
team and published on RTD. We can produce a document based on this Style Guide
that is aimed at users outside the Docs team, and also an RST quick reference.

We should also provide standards and procedures for testing documentation.

*************************
Reference Implementations
*************************



======================
Swagger Implementation
======================

There is a
`test implementation<https://stage-edx-analyticsapi.edx.org/docs/>`_ of
Swagger for the Analytics API.

*********************
Rejected Alternatives
*********************

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

**************
Change History
**************

A list of dated sections that describes a brief summary of each revision of the
OEP.
