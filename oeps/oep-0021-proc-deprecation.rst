=================================
OEP-0021: Deprecation and Removal
=================================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-0021 </oeps/oep-0021-proc-deprecation.rst>`  |
+-----------------+--------------------------------------------------------+
| Title           | Deprecation and Removal                                |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2018-05-25                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Greg Sham <gsham@edx.org>                              |
|                 | Nimisha Asthagiri <nimisha@edx.org>                    |
+-----------------+--------------------------------------------------------+
| Arbiter         | Diana Huang <dhuang@edx.org>                           |
+-----------------+--------------------------------------------------------+
| Status          | Draft                                                  |
+-----------------+--------------------------------------------------------+
| Type            | Process                                                |
+-----------------+--------------------------------------------------------+
| Created         | 2018-05-18                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | TBD                                                    |
+-----------------+--------------------------------------------------------+

Abstract
========

Open edX needs a formal process on how to deprecate and remove unused or
unneeded technology.

This document details that process.

Motivation
==========

There currently is no formal process on how to identify and deprecate
technology that can be removed from the Open edX platform. As a result, the
state of such technologies that could be removed or are already partially
removed is captured in a non-standardized set of formats. This document outlines
common language and a process to use when making the decision on what to
deprecate, when it should be done, and the status of execution. The result of
following this process should be alignment and the right degree of involvement
from all stakeholders involved.

Specification
==============

When a team or individual believes that any technology on the Open edX
platform should no longer be used or maintained, the following steps should be
followed.

Criteria and identification
---------------------------

The list of technology that should be considered for depreciation and
removal from the edX platform includes but is not limited to:

- REST APIs

- Apps

- Learner or partner facing features (user interfaces and associated
backend)

- Backend features - infrastructure and integrations

- Feature toggles (multiple code paths)

A general rule to follow is that if it’s code that's used by any Open edX
platform feature then the associated deprecation/removal should be handled by
the process outlined in this OEP.

Once the technology to be deprecated is identified, the originator
should be prepared to communicate the following:

- Short description of what it is

- Where it currently is in use (i.e. repos) within the Open edX
codebase

- Who uses it (e.g. learners,course authors, etc.)

- Target deadlines for deprecation and removal

Deprecation steps
-----------------

1. Identify the technology to be deprecated

2. Communicate out to edX staff and the Open edX community in accordance with
the communication section below.

3. Create a JIRA ticket with the label [LABEL] and [ticket name convention] in
the [JIRA PROJECT].

4. Initiate discussion as needed with the relevant edX team(s) that own the
technology or other stakeholders (e.g. consumers of the technology) on intention
and timeline for removal.

5. Update the ticket with relevant information on the technology (see section
above for suggested details) and anticipated timeline. See [Suggested-Timeline]
section for suggestions on possible timelines. Continue to update the ticket
description with stakeholder feedback as applicable and update to "Accepted"
once it's been decided that there's alignment. 

6. Clearly mark the technology in question as deprecated as described in the 
[Mark-for-Deprecation] section.

7. Once the above steps are done, annotate the JIRA ticket with “Deprecated”
state.

Removal steps
-------------

1. Once an item that is “Deprecated” is ready to be removed in
accordance with the agreed upon timeline, it should be moved to “Removing” 

2. Relevant parties should be kept apprised of any delays or issues that arise
by updating the corresponding JIRA ticket.

3. Once removal is complete, the status should be communicated out to all
relevant parties as an update to the original deprecation thread and discussion
in each channel.

4. The JIRA ticket can be closed out and updated to "Removed" only once all the
above deprecation and removal steps have been completed.

Communication
-------------

Once the determination has been made to deprecate, the originator should do the
folowing for communications:

1. Email the edx-code list  

2. Make an announcement in the #general channel in the Open edX Slack team. 

3. If originating within edX, an additional email should be sent to the
engineering all list. 

A message template with suggested topics to cover is below.

Note: This template is oriented towards an email announcement to the
edx-code mailing list but relevant portions can be repurposed for other
channels such as the Open edX Slack team, internal edX mailing list and
others.

   From: Deprecation originator

   To: edx-code@googlegroups

   Subject: Deprecation Announcement: [*Technology Name*]

   Body: 
         This message is to inform the Open edX community that we intend to
         deprecate and remove [*Technology Name*] from the Open edX platform.
         This technology currently does the following: [*Description*] and is
         found in the following places [*Repo name(s) or other*]. Deprecation of
         this techology will affect the following users: [*Affected users*]

         We expect that this process will happen over the following timeline:
         [*Timeline*] and if there are any questions or concerns, please reach
         out to [*Point of Contact*].

Status tracking
---------------

.. image:: oep-0021/state-flow.png
   :alt: A diagram that shows the state flow transitions. The process starts in the Proposed state and goes
    through the Communicated, Accepted, Deprecated, Removing, and Removed states. If the proposal isn't Accepted, 
    the state transitions from Proposed to Abandoned.

Possible states (with suggested information to include in JIRA ticket
description):

-  Proposed

   -  Include target dates for each subsequent step.

-  Communicated

   -  Include link to communication on edx-code.

   -  Accept feedback/input from community until target date for “Deprecated”.

   -  Adjust target dates, as needed.

-  Accepted

   -  Community feedback has been addressed and there is alignment on timeline.

-  Deprecated

   - Marked as deprecated according to the [Mark-for-Deprecation] section below.

-  Removing

   -  Include link to communication on edx-code, with a reminder of the removal.

-  Removed

   -  Include links to code removal PRs.

   -  Include link to communication on edx-code that announces removal.

-  Abandoned

   -  Include reason for not continuing to pursue deprecation and removal.

Decision making
---------------

The final decision on deprecation and removal rests with the product delivery
team that is carrying out this process. This team will take into account the
feedback gathered during the deprecation and removal process. 

Depending on the specific technology being deprecated, additional decision
review processes may apply. If the originator feels this could be the case then
the Architecture decision 'guidelines
<https://openedx.atlassian.net/wiki/spaces/AC/pages/704512105/Architecture+Decision+and+Communication+Process>`_ should be consulted. 

Mark for Deprecation
--------------------


Marking a technology as deprecated, depends on what is being deprecated,
as described below.

- REST API

   - Update docstring of the API to say “[DEPRECATED] See
<link-to-JIRA-ticket>.”

- Frontend code

   - Add a console.log(“<Feature/App/API name> is deprecated. See
   <link-to-JIRA-ticket>.”) in the JS library such that the log statement is run
   once when the library is used.

- Backend code

   - Add a LOG.warning(“<Feature/App/API name> is deprecated. See
   link-to-JIRA-ticket>.”) in the \__init__.py file or apps.py file of the
   Django app - such that the log statement is run when the app is first
   initialized.

- Feature toggles (multiple code paths)

   - Set “Expiration Date” as described in `OEP-17. 
   http://open-edx-proposals.readthedocs.io/en/latest
   oep-0017-bp-feature-toggles.html>`_

- xBlock

   - TBD to eventually remove the code for Open edX.

   - See `deprecating xBlock for the edX website
   <https://openedx.atlassian.net/wiki/spaces/ENG/pages/723550424/Deprecating+and+Disabling+an+XBlock+for+the+edX+website>`_ specifically for edx.org.

- Github repo

   - See `OEP-14 <http://open-edx-proposals.readthedocs.io/en/latest/oep-0014-proc-archive-repos.html>`_

Timeline
--------

The actual removal process is highly contextual based on the technology
and team so specific requirements are not provided in this OEP. The
general guidance is that the process should be done in a timely manner
and a suggested timeline is shown in Diagram 2 [LINK HERE]. 

Consider the timing of Open edX named releases when establishing timeline and
carrying out deprecation and removal. `OEP-10
<http://open-edx-proposals.readthedocs.io/en/latest/oep-0010-proc-openedx-releases.html>`_
is the relevant reference for these named releases. If applicable, ensure that
there is a reasonable migration path for the previous named release.

.. image:: oep-0021/timeline.png
   :alt: A diagram that suggests having a 2 week time period between the Proposed
    and Accepted states, giving the community enough time to provide feedback. After
    which, the deprecation and removal transition periods will vary by the type and
    scope of the technical change.

*TURN BELOW INTO A DIAGRAM*

-  **Proposed** - Day 1

-  **Communicated** - Day 2->Day 8

-  **Deprecated** - Day 9 (7-14 days after initial communication,
      depending on influx of feedback)

-  **Under Migration (optional?)** - Day 10-> “next Named Release”

-  **Begun Removal** - “next Named Release” + Day 1 (or when it comes up
      in the team’s sprint)

-  **Removed** - “next Named Release” + Day 7 (or however long it takes
      for the team to remove the code)

Rationale
=========

Change History
==============



