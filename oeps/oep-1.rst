=================================
OEP-1: OEP Purpose and Guidelines
=================================

+--------+---------------------------------------+
|OEP     | :doc:`oep-1`                          |
+--------+---------------------------------------+
|Title   | OEP Purpose and Guidelines            |
+--------+---------------------------------------+
|Author  | Calen Pennington                      |
+--------+---------------------------------------+
|Status  | Draft                                 |
+--------+---------------------------------------+
|Type    | Process                               |
+--------+---------------------------------------+
|Created | 26-Mar-2016                           |
+--------+---------------------------------------+
|Arbiter |                                       |
+--------+---------------------------------------+


What is an OEP?
===============

OEP (pronounced "oh-epp") stands for Open edX (Enhancement) Proposal. An OEP is a
document that specifies a specific choice being made by the
Open edX community, in the form of a best-practice, architecture
design, technology decision, or process adjustment. It should
provide the use-cases and rationales that surround that choice.
OEPs are not the only way for a change to be made to Open edX,
however the goal is for it to be a repository collecting the
history of large and/or broadly relevant choices. 

OEP Types
=========
* A Process proposal describes a change to how the Open edX community
  functions.
* A "Best Practices" proposal describes a technology or implementation
  choice that the Open edX community believes all Open edX services
  and/or libraries should use or follow.
* An Architecture proposal describes relationships between Open edX
  services and libraries, including their APIs and data formats.

Workflow
========

Submitting an OEP
-----------------
The OEP process begins with a new idea for Open edX. It is highly
recommended that a single OEP contain a single key proposal or new idea.
Small enhancements or patches often don't need an OEP and can be injected
into the Open edX development workflow with a patch submission.

Each OEP must have a champion -- someone who writes the OEP using the
style and format described below, shepherds the discussions in the
appropriate forums, and attempts to build community consensus around
the idea. The OEP champion (a.k.a. Author) should first attempt to
ascertain whether the idea is appropriate for an OEP. Posting to the
`edx-code`_ mailing list, ``arch@edx.org`` and ``archexternal@edx.org``
is the best way to go about this.

Vetting an idea publicly before going as far as writing an OEP is meant
to save the potential author time. Many ideas have been brought forward
for changing Open edX that have been rejected for various reasons. Asking
the Open edX community first if an idea is original helps prevent too much
time being spent on something that is guaranteed to be rejected based on
prior discussions (searching the internet does not always do the trick).
It also helps to make sure the idea is applicable to the entire community
and not just the author. Just because an idea sounds good to the author
does not mean it will work for most Open edX installations.

Once the champion has asked the Open edX community as to whether an idea
has any chance of acceptance, a draft OEP should be submitted as a pull request
against the `central OEP repository`_. The pull request title must start
with "OEP-XXX", and claims that OEP number for the included proposal.
Before claiming a number, check the list of previous pull requests to
see which numbers have already been assigned.

.. _central OEP repository: http://github.com/edx/open-edx-proposals

As updates are necessary, the OEP author can update the pull request.

OEPs can be reference pull requests to other Open edX repositiories which
will act as reference implementations.

OEP Review & Resolution
-----------------------

Once the authors have completed an OEP, they may request a review from
OEP reviewers. These reviewers will select one member to act as the final
arbiter on the OEP. This arbiter will be recorded in the "Arbiter"
header on the OEP. The remaining reviewers and the rest of the Open edX
community will be given the opportunity to comment on the OEP, with the
Arbiter serving to keep the discussion on track and to evaluate when
it has reached a final conclusion.

For an OEP to be accepted by the Arbiter, it must meet certain minimum
criteria. It must be a clear and complete description of the proposed
enhancement. The enhancement must represent a net improvement. The proposed
implementation, if applicable, must pass the existing Open edX review process.

An OEP can be assigned the status "Deferred". The OEP author or Arbiter can
assign the OEP this status when no progress is being made on the OEP. Once a
OEP is deferred, the OEP Author can re-assign it to draft status.

An OEP can also be "Rejected" by the Arbiter. Perhaps after all is said and
done it was not a good idea. It is still important to have a record of this
fact. The "Withdrawn" status is similar - it means that the OEP author
themselves has decided that the OEP is actually a bad idea, or has accepted
that a competing proposal is a better alternative.

When a OEP is Accepted, Rejected or Withdrawn, the OEP should be updated
accordingly. In addition to updating the status field, at the very least the
Resolution header should be added with a link to the relevant post in the edx-code
mailing list archive or to the appropriate section of the PR.

OEPs can also be superseded by a different OEP, rendering the original
obsolete.

The possible paths of the status of OEPs are as follows:

.. image:: oep-1/state-flow.png

Please note that OEP statuses do not necessary coincide with the status of
the pull request that contains the OEP. For example, OEPs that have been "Rejected"
should still be merged, but should be marked as Rejected. This preserves the rationale
and description of the OEP in the generated documentation.

Likewise, an OEP that is in "Draft" may be merged to capture a set of edits,
and to make the proposal more visible to community comment. From that point, additional
pull requests can be opened to edit the "Draft" OEP, until it converges to being
either "Accepted" or "Rejected".

OEP Maintenance
---------------

In general, OEPs are no longer modified after they have reached the Final state.
They can be replaced by subsequent OEPs, however (OEPs that have been replaced
are given the status "Replaced").

What belongs in a successful OEP?
=================================
Each OEP should have the following parts:

*Preamble*
    A table containing meta-data about the OEP, including the OEP number,
    a short descriptive title, the names, and optionally the contact info for each author, etc.

*Abstract*
    A short (~200 word) description of the technical issue being addressed.

*Copyright*
    All OEPs must be shared under the `Creative Commons Attribution-ShareAlike 4.0 International License`_.

.. _Creative Commons Attribution-ShareAlike 4.0 International License: https://creativecommons.org/licenses/by-sa/4.0/

*Specification*
    The technical specification should describe the syntax and semantics of any new API,
    or the details of what the Best Practice, Process, or Archictecture being proposed
    by the OEP are.

*Motivation*
    The motivation is critical for OEPs that want to change Open edX. It should
    clearly explain why the existing achictecture or process is inadequate to
    address the problem that the OEP solves, or why Open edX would be significantly
    improved by adopting the best practice. OEP submissions without sufficient
    motivation are unlikely to be accepted.

*Rationale*
    The rationale fleshes out the specification by describing what motivated the
    design and why particular design decisions were made. It should describe
    alternate designs that were considered and related work, for example, how the
    feature is supported in other systems.

    The rationale should provide evidence of consensus within the community
    and discuss important objections or concerns raised during discussion.
    It should also link to any major and pertinent discussions of the OEP
    that happened in other fora (such as the `edx-code`_ mailing list).

    .. _edx-code: https://groups.google.com/forum/#!forum/edx-code

*Backwards Compatibility*
    All OEPs that introduce backwards incompatibilities must include a section
    describing these incompatibilities and their severity. The OEP must explain
    how the author proposes to deal with these incompatibilities. OEP submissions
    without that don't consider backwards compatibility are unlikely to be
    accepted.

*Reference Implementation*
    The reference implementation must be completed before any OEP is given status
    "Final", but it need not be completed before the OEP is accepted. While there
    is merit to the approach of reaching consensus on the specification and rationale
    before writing code, the principle of "rough consensus and running code" is still
    useful when it comes to resolving many discussions of API details.

    The final implementation must include test code and documentation, following the
    `Open edX Contribution Guidelines`_.

.. _Open edX Contribution Guidelines: http://edx.readthedocs.org/projects/edx-developer-guide/en/latest/process/index.html

*Rejected Alternatives*

    The OEP should list any alternative designs or implementations that were
    considered and rejected, and why they weren't chosen. It should also link
    to the original source of that discussion.


OEP Formats and Templates
-------------------------

OEPs are UTF-8 encoded text files using the `reStructuredText`_ format.
ReStructuredText [8] allows for rich markup that is still quite easy to read,
but also results in good-looking and functional HTML. OEPs are rendered to HTML
using Sphinx. An OEP template can be found `in the repo`_.

.. _reStructuredText: http://docutils.sourceforge.net/rst.html
.. _in the repo: https://github.com/cpennington/open-edx-proposals/blob/master/oep-template.rst

OEP Header Preamble
-------------------
Each OEP must begin with an ReST table with meta-data about the OEP. The rows must
appear in the following order. Rows in italics are optional and
are described below. All other rows are required.


+---------------+---------------------------------------------+
| OEP           | <link to OEP>                               |
+---------------+---------------------------------------------+
| Title         | <OEP title>                                 |
+---------------+---------------------------------------------+
| Last-Modified | <date string>                               |
+---------------+---------------------------------------------+
| Author        | <list of authors' real names and            |
|               | email addresses>                            |
+---------------+---------------------------------------------+
| Arbiter       | <OEP Arbiter's real name and email address> |
+---------------+---------------------------------------------+
| Status        | <Draft | Accepted | Deferred |              |
|               | Rejected | Withdrawn | Final |              |
|               | Replaced>                                   |
+---------------+---------------------------------------------+
| Type          | <Architecture | Best Practice |             |
|               | Process>                                    |
+---------------+---------------------------------------------+
|  Created      | <date created on, in yyyy-mm-dd format>     |
+---------------+---------------------------------------------+
| `Replaces`    | <link to OEP>                               |
+---------------+---------------------------------------------+
| `Replaced-By` | <link to OEP>                               |
+---------------+---------------------------------------------+

The Author header lists the names, and optionally the email addresses of
all the authors/owners of the OEP. The format of the Author header value must be
``Random J. User <address@dom.ain>`` if the email address is included, and just
``Random J. User`` if the address is not given. If there are multiple authors,
they should be put in a comma separated list.

The Arbiter field is used to record who the final decision to approve or
reject an OEP rests with.

The Type header specifies the type of OEP: Architecture, Best Practice or Process.

The Created header records the date that the OEP was assigned a number. It should be
in yyyy-mm-dd format, e.g. 2016-04-21.

OEPs may also have a Replaced-By header indicating that a OEP has been rendered
obsolete by a later document; the value is the number of the OEP that replaces
the current document. The newer OEP must have a Replaces header containing the
number of the OEP that it rendered obsolete.

Auxiliary Files
---------------

OEPs may include auxiliary files such as diagrams. Such files must be named put in the
directory oep-XXXX/, where "XXXX" is the OEP number.

Reporting OEP Bugs, or Submitting OEP Updates
---------------------------------------------
Bugs and updates to an OEP can be submitted as comments on the OEP PR, or by
making your own PR against the branch containing the original OEP PR. Whether
those changes are included is up to the author of the OEP.

Transferring OEP Ownership
--------------------------
It occasionally becomes necessary to transfer ownership of OEPs to a new champion.
In general, it is preferable to retain the original author as a co-author of the
transferred OEP, but that's really up to the original author. A good reason to
transfer ownership is because the original author no longer has the time or interest
in updating it or following through with the OEP process, or has fallen off the face
of the 'net (i.e. is unreachable or not responding to email). A bad reason to transfer
ownership is because the author doesn't agree with the direction of the OEP. One aim
of the OEP process is to try to build consensus around a OEP, but if that's not possible,
an author can always submit a competing OEP.

