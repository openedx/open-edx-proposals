=================================
OEP-0021: Deprecation and Removal
=================================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-0021 <oep-0021-proc-deprecation>`            |
+-----------------+--------------------------------------------------------+
| Title           | Deprecation and Removal                                |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2018-06-21                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Greg Sham <gsham@edx.org>                              |
|                 | Nimisha Asthagiri <nimisha@edx.org>                    |
+-----------------+--------------------------------------------------------+
| Arbiter         | Diana Huang <dhuang@edx.org>                           |
+-----------------+--------------------------------------------------------+
| Status          | Accepted                                               |
+-----------------+--------------------------------------------------------+
| Type            | Process                                                |
+-----------------+--------------------------------------------------------+
| Created         | 2018-05-18                                             |
+-----------------+--------------------------------------------------------+
| Review Period   | 2018-06-06 - 2018-06-20                                |
+-----------------+--------------------------------------------------------+
| Resolution      | `open-edx-proposals#63 resolution`_                    |
+-----------------+--------------------------------------------------------+
| References      |                                                        |
+-----------------+--------------------------------------------------------+

.. _open-edx-proposals#63 resolution: https://github.com/edx/open-edx-proposals/pull/63#pullrequestreview-130885305

Abstract
========

Open edX needs a formal process on how to deprecate and remove unused or
unneeded technology.

This document details that process.

Motivation
==========

There currently is no formal process on how to identify and deprecate
technology that can be removed from the Open edX platform. As a result, the
state of technologies that can be removed or are already partially
removed is captured in a non-standardized set of formats. This document outlines
common language and a process to use when making the decision on what to
deprecate, when it should be done, and the status of execution. The result of
following this process should be alignment and the right degree of involvement
from all stakeholders.

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

- Learner or partner facing features (user interfaces and associated backend)

- Backend features - infrastructure and integrations

- Feature toggles (multiple code paths)

A general rule to follow is that if it’s code that's used by any Open edX
platform feature then the associated deprecation/removal should be handled by
the process outlined in this OEP.

Once the technology to be deprecated is identified, the originator - whether an
edX staff member or Open edX community member - should be prepared to
communicate the following:

- Short description of what the technology is

- Where it's currently in use (i.e. repos) within the Open edX codebase

- Who uses it (e.g. learners, course authors, etc.)

- Target deadlines for deprecation and removal

Deprecation steps
-----------------

1. Identify the technology to be deprecated.

2. Communicate out to edX staff and the Open edX community in accordance with
the communication section below.

3. Create a JIRA ticket of issue type [Task] with the label 
[deprecation-removal]. After creation, confirm that the ticket shows up in the
Deprecation/Removal JIRA
`board <https://openedx.atlassian.net/secure/RapidBoard.jspa?rapidView=452>`_

*Note: A custom state and workflow is being setup for this board.
If this is not available yet, then the status can be kept as "In Progress" and
appropriate status from this OEP can be updated in the description field. If
there are any issues viewing the board or creating/editing a ticket as an Open
edX community member, then contact either of the authors for assistance. This
note will be removed once the JIRA workflow has been established.*

4. Initiate discussion as needed with the relevant edX team(s) that own the
technology or other stakeholders on intention and timeline for removal. The
other stakeholders (e.g. consumers of the technology) are not limited to 
other internal edX teams but could include the Open edX Community, edX Partners,
and Enterprise clients. 

5. Update the ticket with relevant information (see section above for suggested
details) and anticipated timeline. See [Suggested-Timeline] section for
suggestions on possible timelines. Continue to update the ticket description
with stakeholder feedback as applicable and update to "Accepted" once it's been
decided that there's alignment. 

6. Clearly mark the technology in question as deprecated as described in the 
[Mark-for-Deprecation] section. If relevant documentation exists on the Open edX
`documentation site <http://docs.edx.org/>`_ then contact the documentation team
(docs@edx.org) to coordinate follow-up action for the deprecation and removal being
performed.

7. Once the above steps are done, move the JIRA ticket to the “Deprecated”
state.

*Note: There is consideration being given to maintaining a central .rst file
documenting the technologies that have been deprecated. If implemented as part
of a separate OEP, this section will be updated with a reference to that process
and an additional step on how to update that file.*

Removal steps
-------------

1. Once an item that is “Deprecated” is ready to be removed in
accordance with the agreed upon timeline, it should be moved to “Removing”. 

2. Relevant parties should be kept apprised of any delays or issues that arise
by updating the corresponding JIRA ticket description.

3. Once removal is complete, the status should be communicated out to all
relevant parties as an update to the original deprecation thread and discussion
in each channel.

4. The JIRA ticket can be closed out and updated to "Removed" only once all the
above deprecation and removal steps have been completed.

5. At any point, if the decision is made to stop the process, the ticket should
be moved to the "Abandoned" state with the reason added to the description.

Communication
-------------

Once the determination has been made to deprecate, the originator should do the
following for communications:

1. Email the `edx-code list. <https://groups.google.com/forum/#!forum/edx-code>`_
In the event that the edx-code list has been deprecated, its designated
replacement channel should be utilized instead.

2. Make an announcement in the **#architecture** channel in the Open edX Slack
team. The announcement should be shared to **#general** as well. 

3. If originating within edX, an additional message should be sent to the
engineering-all email list. 

A message template with suggested topics to cover is below.

*Note: This template is oriented towards an email announcement to the
edx-code mailing list but relevant portions can be repurposed for other
channels such as the Open edX Slack team, internal edX mailing list and
others.*

   From: Deprecation originator

   To: edx-code@googlegroups.com

   Subject: Deprecation Announcement: [*Technology Name*]

   Body: 
         This message is to inform the Open edX community that we intend to
         deprecate and remove [*Technology Name*] from the Open edX platform.
         This technology currently does the following: [*Description*] and is
         found in the following places [*Repo name(s) or other locations*].
         Deprecation of this techology will affect the following users: 
         [*Affected users*]

         We expect that this process will happen over the following timeline:
         [*Timeline*] and if there are any questions or concerns, please reach
         out to [*Point of Contact*].

Status tracking
---------------

.. image:: oep-0021/state-flow.png
   :alt: A diagram that shows the state flow transitions. The process starts in the Proposed state and goes
    through the Communicated, Accepted, Deprecated, Removing, and Removed states. If the proposal isn't Accepted, 
    the state transitions from Proposed to Abandoned.

JIRA ticket states (with suggested information to include in ticket
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
the Architecture decision `guidelines
<https://openedx.atlassian.net/wiki/spaces/AC/pages/704512105/Architecture+Decision+and+Communication+Process>`_
should be consulted.

Mark for Deprecation
--------------------


How to mark a technology as deprecated depends on what is being deprecated,
as described below.

- REST API

   - Update docstring of the API to say *[DEPRECATED] See <link-to-JIRA-ticket>*.

- Frontend code

   - Add a console.log("<Feature/App/API name> is deprecated. See <link-to-JIRA-ticket>.") in the JS library such that the log statement is run once when the library is used.

- Backend code

   - Add a LOG.warning("<Feature/App/API name> is deprecated. See <link-to-JIRA-ticket>.") in the \__init__.py file or apps.py file of the Django app - such that the log statement is run when the app is first initialized.

- Feature toggles (multiple code paths)

   - Set “Expiration Date” as described in `OEP-17 <http://open-edx-proposals.readthedocs.io/en/latest/oep-0017-bp-feature-toggles.html>`_.

- xBlock

   - TBD to eventually remove the code for Open edX.
   - See `deprecating xBlock for the edX website <https://openedx.atlassian.net/wiki/spaces/ENG/pages/723550424/Deprecating+and+Disabling+an+XBlock+for+the+edX+website>`_ specifically for edx.org.

- Github repo
   - See `OEP-14 <http://open-edx-proposals.readthedocs.io/en/latest/oep-0014-proc-archive-repos.html>`_


Timeline
--------

The actual removal process is highly contextual based on the technology
and team so specific requirements are not provided in this OEP. The
general guidance is that the process should be done in a timely manner
and a suggested timeline is shown in the diagram below. 

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

-  **Proposed** - Day 1

-  **Communicated** - Day 2->Day 13

-  **Accepted** - Day 14 (7-14 days after initial communication, depending on influx of feedback) 

-  **Deprecated/Removing/Removed** - Day 15 and onwards (Time that these steps take will be dependent on the technology involved)

Change History
==============