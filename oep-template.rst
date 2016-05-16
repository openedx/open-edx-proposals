=====================
OEP-XXX: OEP Template
=====================

.. This is a template that can be used when starting a new OEP

+---------------+-------------------------------------------+
| OEP           | :doc:`oep-XXX`                            |
+---------------+-------------------------------------------+
| Title         | <OEP title>                               |
+---------------+-------------------------------------------+
| Last-Modified | <date string>                             |
+---------------+-------------------------------------------+
| Author        | <list of authors' real names and          |
|               | optionally, email addrs>                  |
+---------------+-------------------------------------------+
| Arbiter       | <OEP czar's real name>                    |
+---------------+-------------------------------------------+
| Status        | <Draft | Accepted | Deferred |            |
|               | Rejected | Withdrawn | Final |            |
|               | Replaced>                                 |
+---------------+-------------------------------------------+
| Type          | <Architecture | Best Practice |           |
|               | Process>                                  |
+---------------+-------------------------------------------+
|  Created      | <date created on, in YYYY-MM-DD format>   |
+---------------+-------------------------------------------+
| `Replaces`    | <OEP number>                              |
+---------------+-------------------------------------------+
| `Replaced-By` | <OEP number>                              |
+---------------+-------------------------------------------+

Abstract
========

A short (~200 word) description of the technical issue being addressed.


Specification
=============

The technical specification should describe the syntax and semantics of any new API,
or the details of what the Best Practice, Process, or Architecture being proposed
by the OEP are.


Motivation
==========

The motivation is critical for OEPs that want to change Open edX. It should
clearly explain why the existing achictecture or process is inadequate to
address the problem that the OEP solves, or why Open edX would be significantly
improved by adopting the best practice. OEP submissions without sufficient
motivation are unlikely to be accepted.



Rationale
=========

The rationale fleshes out the specification by describing what motivated the
design and why particular design decisions were made. It should describe
alternate designs that were considered and related work, for example, how the
feature is supported in other systems.

The rationale should provide evidence of consensus within the community
and discuss important objections or concerns raised during discussion.
It should also link to any major and pertinent discussions of the OEP
that happened in other fora (such as the `edx-code`_ mailing list).

.. _edx-code: https://groups.google.com/forum/#!forum/edx-code


Backwards Compatibility
=======================

All OEPs that introduce backwards incompatibilities must include a section
describing these incompatibilities and their severity. The OEP must explain
how the author proposes to deal with these incompatibilities. OEP submissions
without that don't consider backwards compatibility are unlikely to be
accepted.


Reference Implementation
========================

The reference implementation must be completed before any OEP is given status
"Final", but it need not be completed before the OEP is accepted. While there
is merit to the approach of reaching consensus on the specification and rationale
before writing code, the principle of "rough consensus and running code" is still
useful when it comes to resolving many discussions of API details.

The final implementation must include test code and documentation, following the
`Open edX Contribution Guidelines`_.

.. _Open edX Contribution Guidelines: http://edx.readthedocs.org/projects/edx-developer-guide/en/latest/process/index.html


Rejected Alternatives
=====================

The OEP should list any alternative designs or implementations that were
considered and rejected, and why they weren't chosen. It should also link
to the original source of that discussion.


Copyright
=========

.. image:: https://i.creativecommons.org/l/by-sa/4.0/88x31.png
    :alt: Creative Commons License CC-BY-SA
    :target: http://creativecommons.org/licenses/by-sa/4.0/

This work is licensed under a `Creative Commons Attribution-ShareAlike 4.0 International License`_.

.. _Creative Commons Attribution-ShareAlike 4.0 International License: https://creativecommons.org/licenses/by-sa/4.0/


