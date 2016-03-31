OEP-1: OEP Purpose and Guidelines
=================================

+--------+---------------------------------------+
|OEP     | :doc:`oep-1`                          |
+--------+---------------------------------------+
|Title   | OEP Purpose and Guidelines            |
+--------+---------------------------------------+
|Author  | Calen Pennington                      |
+--------+---------------------------------------+
|Status  | Draft (:oep-status:`1`)               |
+--------+---------------------------------------+
|Type    | Process                               |
+--------+---------------------------------------+
|Created | 26-Mar-2016                           |
+--------+---------------------------------------+

What is an OEP?
===============

OEP stands for Open edX (Enhancement) Proposal. An OEP is a document that specifies a specific choice being made by the Open edX community, in the form of a best-practice, architecture design, technology decision, or process adjustment. It should provide the use-cases and rationales that surround that choice.
OEPs are not the only way for a change to be made to Open edX, however the goal is for it to be a repository collecting the history of large and/or broadly relevant choices. 

OEP Types
=========
* A Process proposal describes a change to how the Open edX community functions.
* A "Best Practices" proposal describes a technology or implementation choice that the Open edX community believes all Open edX services and/or libraries should use or follow.
* An Architecture proposal describes relationships between Open edX services and libraries, including their APIs and data formats.

Workflow
========

Submitting an OEP
-----------------
The OEP process begins with a new idea for Open edX. It is highly recommended that a single OEP contain a single key proposal or new idea. Small enhancements or patches often don't need an OEP and can be injected into the Open edX development workflow with a patch submission﻿.
Each OEP must have a champion -- someone who writes the OEP using the style and format described below, shepherds the discussions in the appropriate forums, and attempts to build community consensus around the idea. The OEP champion (a.k.a. Author) should first attempt to ascertain whether the idea is OEP-able. Posting to the edx-code mailing list is the best way to go about this.

The more focused the OEP, the more successful it . The PEP editors reserve the right to reject PEP proposals if they appear too unfocused or too broad. If in doubt, split your PEP into several well-focused ones.

Vetting an idea publicly before going as far as writing a PEP is meant to save the potential author time. Many ideas have been brought forward for changing Python that have been rejected for various reasons. Asking the Python community first if an idea is original helps prevent too much time being spent on something that is guaranteed to be rejected based on prior discussions (searching the internet does not always do the trick). It also helps to make sure the idea is applicable to the entire community and not just the author. Just because an idea sounds good to the author does not mean it will work for most people in most areas where Python is used.
Once the champion has asked the Python community as to whether an idea has any chance of acceptance, a draft PEP should be presented to python-ideas. This gives the author a chance to flesh out the draft PEP to make properly formatted, of high quality, and to address initial concerns about the proposal.
Following a discussion on python-ideas, the proposal should be sent as a draft PEP to the PEP editors < peps@python.org >. The draft must be written in PEP style as described below, else it will be sent back without further regard until proper formatting rules are followed (although minor errors will be corrected by the editors).
If the PEP editors approve, they will assign the PEP a number, label it as Standards Track, Informational, or Process, give it status "Draft", and create and check-in the initial draft of the PEP. The PEP editors will not unreasonably deny a PEP. Reasons for denying PEP status include duplication of effort, being technically unsound, not providing proper motivation or addressing backwards compatibility, or not in keeping with the Python philosophy. The BDFL can be consulted during the approval phase, and is the final arbiter of the draft's PEP-ability.
Developers with hg push privileges for the PEP repository [10] may claim PEP numbers directly by creating and committing a new PEP. When doing so, the developer must handle the tasks that would normally be taken care of by the PEP editors (seePEP Editor Responsibilities & Workflow ). This includes ensuring the initial version meets the expected standards for submitting a PEP. Alternately, even developers may choose to submit PEPs through the PEP editors. When doing so, let the PEP editors know you have hg push privileges and they can guide you through the process of updating the PEP repository directly.
As updates are necessary, the PEP author can check in new versions if they (or a collaborating developer) have hg push privileges, or else they can email new PEP versions to the PEP editors for publication.
After a PEP number has been assigned, a draft PEP may be discussed further on python-ideas (getting a PEP number assigned early can be useful for ease of reference, especially when multiple draft PEPs are being considered at the same time). Eventually, all Standards Track PEPs must be sent to the python-dev list for review as described in the next section.
Standards Track PEPs consist of two parts, a design document and a reference implementation. It is generally recommended that at least a prototype implementation be co-developed with the PEP, as ideas that sound good in principle sometimes turn out to be impractical when subjected to the test of implementation.
PEP authors are responsible for collecting community feedback on a PEP before submitting it for review. However, wherever possible, long open-ended discussions on public mailing lists should be avoided. Strategies to keep the discussions efficient include: setting up a separate SIG mailing list for the topic, having the PEP author accept private comments in the early design phases, setting up a wiki page, etc. PEP authors should use their discretion here.
PEP Review & Resolution
Once the authors have completed a PEP, they may request a review for style and consistency from the PEP editors. However, the content and final acceptance of the PEP must be requested of the BDFL, usually via an email to the python-dev mailing list. PEPs are reviewed by the BDFL and his chosen consultants, who may accept or reject a PEP or send it back to the author(s) for revision. For a PEP that is predetermined to be acceptable (e.g., it is an obvious win as-is and/or its implementation has already been checked in) the BDFL may also initiate a PEP review, first notifying the PEP author(s) and giving them a chance to make revisions.
The final authority for PEP approval is the BDFL. However, whenever a new PEP is put forward, any core developer that believes they are suitably experienced to make the final decision on that PEP may offer to serve as the BDFL's delegate (or "PEP czar") for that PEP. If their self-nomination is accepted by the other core developers and the BDFL, then they will have the authority to approve (or reject) that PEP. This process happens most frequently with PEPs where the BDFL has granted in principle approval for something to be done, but there are details that need to be worked out before the PEP can be accepted.
If the final decision on a PEP is to be made by a delegate rather than directly by the BDFL, this will be recorded by including the "BDFL-Delegate" header in the PEP.
PEP review and resolution may also occur on a list other than python-dev (for example, distutils-sig for packaging related PEPs that don't immediately affect the standard library). In this case, the "Discussions-To" heading in the PEP will identify the appropriate alternative list where discussion, review and pronouncement on the PEP will occur.
For a PEP to be accepted it must meet certain minimum criteria. It must be a clear and complete description of the proposed enhancement. The enhancement must represent a net improvement. The proposed implementation, if applicable, must be solid and must not complicate the interpreter unduly. Finally, a proposed enhancement must be "pythonic" in order to be accepted by the BDFL. (However, "pythonic" is an imprecise term; it may be defined as whatever is acceptable to the BDFL. This logic is intentionally circular.) See PEP 2 [2] for standard library module acceptance criteria.
Once a PEP has been accepted, the reference implementation must be completed. When the reference implementation is complete and incorporated into the main source code repository, the status will be changed to "Final".
A PEP can also be assigned status "Deferred". The PEP author or an editor can assign the PEP this status when no progress is being made on the PEP. Once a PEP is deferred, a PEP editor can re-assign it to draft status.
A PEP can also be "Rejected". Perhaps after all is said and done it was not a good idea. It is still important to have a record of this fact. The "Withdrawn" status is similar - it means that the PEP author themselves has decided that the PEP is actually a bad idea, or has accepted that a competing proposal is a better alternative.
When a PEP is Accepted, Rejected or Withdrawn, the PEP should be updated accordingly. In addition to updating the status field, at the very least the Resolution header should be added with a link to the relevant post in the python-dev mailing list archives.
PEPs can also be superseded by a different PEP, rendering the original obsolete. This is intended for Informational PEPs, where version 2 of an API can replace version 1.
The possible paths of the status of PEPs are as follows:
pep-0001-1.png
Some Informational and Process PEPs may also have a status of "Active" if they are never meant to be completed. E.g. PEP 1(this PEP).
PEP Maintenance
In general, Standards track PEPs are no longer modified after they have reached the Final state. Once a PEP has been completed, the Language and Standard Library References become the formal documentation of the expected behavior.
Informational and Process PEPs may be updated over time to reflect changes to development practices and other details. The precise process followed in these cases will depend on the nature and purpose of the PEP being updated.
What belongs in a successful PEP?
Each PEP should have the following parts:
Preamble -- RFC 822 style headers containing meta-data about the PEP, including the PEP number, a short descriptive title (limited to a maximum of 44 characters), the names, and optionally the contact info for each author, etc.
Abstract -- a short (~200 word) description of the technical issue being addressed.
Copyright/public domain -- Each PEP must either be explicitly labeled as placed in the public domain (see this PEP as an example) or licensed under the Open Publication License [7] .
Specification -- The technical specification should describe the syntax and semantics of any new language feature. The specification should be detailed enough to allow competing, interoperable implementations for at least the current major Python platforms (CPython, Jython, IronPython, PyPy).
Motivation -- The motivation is critical for PEPs that want to change the Python language. It should clearly explain why the existing language specification is inadequate to address the problem that the PEP solves. PEP submissions without sufficient motivation may be rejected outright.
Rationale -- The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages.
The rationale should provide evidence of consensus within the community and discuss important objections or concerns raised during discussion.
Backwards Compatibility -- All PEPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The PEP must explain how the author proposes to deal with these incompatibilities. PEP submissions without a sufficient backwards compatibility treatise may be rejected outright.
Reference Implementation -- The reference implementation must be completed before any PEP is given status "Final", but it need not be completed before the PEP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.
The final implementation must include test code and documentation appropriate for either the Python language reference or the standard library reference.
PEP Formats and Templates
PEPs are UTF-8 encoded text files using the reStructuredText [8] format. ReStructuredText [8] allows for rich markup that is still quite easy to read, but also results in good-looking and functional HTML. PEP 12 contains instructions and a template [4]for reStructuredText PEPs.
A Python script automatically converts PEPs to HTML for viewing on the web [5] . The conversion of reStructuredText PEPs is handled by the Docutils [9] module; the same script also renders a legacy plain-text format of PEP internally, to support pre-reST documents.
PEP Header Preamble
Each PEP must begin with an RFC 822 style header preamble. The headers must appear in the following order. Headers marked with "*" are optional and are described below. All other headers are required.
  PEP: <pep number>
  Title: <pep title>
  Version: <version string>
  Last-Modified: <date string>
  Author: <list of authors' real names and optionally, email addrs>
* BDFL-Delegate: <PEP czar's real name>
* Discussions-To: <email address>
  Status: <Draft | Active | Accepted | Deferred | Rejected |
           Withdrawn | Final | Superseded>
  Type: <Standards Track | Informational | Process>
* Content-Type: <text/plain | text/x-rst>
* Requires: <pep numbers>
  Created: <date created on, in dd-mmm-yyyy format>
* Python-Version: <version number>
  Post-History: <dates of postings to python-list and python-dev>
* Replaces: <pep number>
* Superseded-By: <pep number>
* Resolution: <url>
The Author header lists the names, and optionally the email addresses of all the authors/owners of the PEP. The format of the Author header value must be
Random J. User < address@dom.ain >
if the email address is included, and just
Random J. User
if the address is not given. For historical reasons the format " address@dom.ain (Random J. User)" may appear in a PEP, however new PEPs must use the mandated format above, and it is acceptable to change to this format when PEPs are updated.
If there are multiple authors, each should be on a separate line following RFC 2822 continuation line conventions. Note that personal email addresses in PEPs will be obscured as a defense against spam harvesters.
The BDFL-Delegate field is used to record cases where the final decision to approve or reject a PEP rests with someone other than the BDFL. (The delegate's email address is currently omitted due to a limitation in the email address masking for reStructuredText PEPs)
Note: The Resolution header is required for Standards Track PEPs only. It contains a URL that should point to an email message or other web resource where the pronouncement about the PEP is made.
For a PEP where final pronouncement will be made on a list other than python-dev, a Discussions-To header will indicate the mailing list or URL where the pronouncement will occur. A temporary Discussions-To header may also be used when a draft PEP is being discussed prior to submission for pronouncement. No Discussions-To header is necessary if the PEP is being discussed privately with the author, or on the python-list, python-ideas or python-dev mailing lists. Note that email addresses in the Discussions-To header will not be obscured.
The Type header specifies the type of PEP: Standards Track, Informational, or Process.
The format of a PEP is specified with a Content-Type header. The acceptable values are "text/plain" for plaintext PEPs (seePEP 9 [3] ) and "text/x-rst" for reStructuredText PEPs (see PEP 12 [4] ). Plaintext ("text/plain") is the default if no Content-Type header is present.
The Created header records the date that the PEP was assigned a number, while Post-History is used to record the dates of when new versions of the PEP are posted to python-list and/or python-dev. Both headers should be in dd-mmm-yyyy format, e.g. 14-Aug-2001.
Standards Track PEPs will typically have a Python-Version header which indicates the version of Python that the feature will be released with. Standards Track PEPs without a Python-Version header indicate interoperability standards that will initially be supported through external libraries and tools, and then supplemented by a later PEP to add support to the standard library. Informational and Process PEPs do not need a Python-Version header.
PEPs may have a Requires header, indicating the PEP numbers that this PEP depends on.
PEPs may also have a Superseded-By header indicating that a PEP has been rendered obsolete by a later document; the value is the number of the PEP that replaces the current document. The newer PEP must have a Replaces header containing the number of the PEP that it rendered obsolete.
Auxiliary Files
PEPs may include auxiliary files such as diagrams. Such files must be named pep-XXXX-Y.ext , where "XXXX" is the PEP number, "Y" is a serial number (starting at 1), and "ext" is replaced by the actual file extension (e.g. "png").
Reporting PEP Bugs, or Submitting PEP Updates
How you report a bug, or submit a PEP update depends on several factors, such as the maturity of the PEP, the preferences of the PEP author, and the nature of your comments. For the early draft stages of the PEP, it's probably best to send your comments and changes directly to the PEP author. For more mature, or finished PEPs you may want to submit corrections to the Python issue tracker [6] so that your changes don't get lost. If the PEP author is a Python developer, assign the bug/patch to them, otherwise assign it to a PEP editor.
When in doubt about where to send your changes, please check first with the PEP author and/or a PEP editor.
PEP authors with hg push privileges for the PEP repository can update the PEPs themselves by using "hg push" to submit their changes.
Transferring PEP Ownership
It occasionally becomes necessary to transfer ownership of PEPs to a new champion. In general, it is preferable to retain the original author as a co-author of the transferred PEP, but that's really up to the original author. A good reason to transfer ownership is because the original author no longer has the time or interest in updating it or following through with the PEP process, or has fallen off the face of the 'net (i.e. is unreachable or not responding to email). A bad reason to transfer ownership is because the author doesn't agree with the direction of the PEP. One aim of the PEP process is to try to build consensus around a PEP, but if that's not possible, an author can always submit a competing PEP.
If you are interested in assuming ownership of a PEP, send a message asking to take over, addressed to both the original author and the PEP editors < peps@python.org >. If the original author doesn't respond to email in a timely manner, the PEP editors will make a unilateral decision (it's not like such decisions can't be reversed :).
PEP Editor Responsibilities & Workflow
A PEP editor must subscribe to the < peps@python.org > list. All correspondence related to PEP administration should be sent (or forwarded) to < peps@python.org > (but please do not cross-post!).
For each new PEP that comes in an editor does the following:
Read the PEP to check if it is ready: sound and complete. The ideas must make technical sense, even if they don't seem likely to be accepted.
The title should accurately describe the content.
Edit the PEP for language (spelling, grammar, sentence structure, etc.), markup (for reST PEPs), code style (examples should match PEP 8 & 7).
If the PEP isn't ready, an editor will send it back to the author for revision, with specific instructions.
Once the PEP is ready for the repository, a PEP editor will:
Assign a PEP number (almost always just the next available number, but sometimes it's a special/joke number, like 666 or 3141). (Clarification: For Python 3, numbers in the 3000s were used for Py3k-specific proposals. But now that all new features go into Python 3 only, the process is back to using numbers in the 100s again. Remember that numbers below 100 are meta-PEPs.)
Add the PEP to a local clone of the PEP repository. For mercurial workflow instructions, follow The Python Developers Guide
The mercurial repo for the peps is:
http://hg.python.org/peps/
Run ./genpepindex.py and ./pep2html.py <PEP Number> to ensure they are generated without errors. If either triggers errors, then the web site will not be updated to reflect the PEP changes.
Commit and push the new (or updated) PEP
Monitor python.org to make sure the PEP gets added to the site properly. If it fails to appear, running make will build all of the current PEPs. If any of these are triggering errors, they must be corrected before any PEP will update on the site.
Send email back to the PEP author with next steps (post to python-list & -dev).
Updates to existing PEPs also come in to peps@python.org . Many PEP authors are not Python committers yet, so PEP editors do the commits for them.
Many PEPs are written and maintained by developers with write access to the Python codebase. The PEP editors monitor the python-checkins list for PEP changes, and correct any structure, grammar, spelling, or markup mistakes they see.
PEP editors don't pass judgment on PEPs. They merely do the administrative & editorial part (which is generally a low volume task).
Copyright
Creative Commons License
This work is licensed under a Creative Commons Attribution 4.0 International License.
