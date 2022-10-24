OEP-0060 Open Source Security Working Group
###########################################

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-60 <oep-0060-proc-sec-group>`                |
+-----------------+--------------------------------------------------------+
| Title           | Open Source Security Working Group                     |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2022-10-25                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Alie Langston <alangston@2u.com>                       |
|                 | Feanil Patel <fpatel@tcril.org>                        |
|                 | Phil Shiu <pshiu@2u.com>                               |
+-----------------+--------------------------------------------------------+
| Arbiter         | Feanil Patel <fpatel@tcril.org>                        |
+-----------------+--------------------------------------------------------+
| Status          | Under Review                                           |
+-----------------+--------------------------------------------------------+
| Type            | Process                                                |
+-----------------+--------------------------------------------------------+
| Created         | 2022-10-25                                             |
+-----------------+--------------------------------------------------------+
| Review Period   | 2022-10-25 - 2022-11-30                                |
+-----------------+--------------------------------------------------------+

Context
*******
edX/2U has a volunteer security working group (SWG) that has historically handled the triage process for any open
source vulnerabilities. This process included digesting emails that were sent to security@edx.org, assessing the
severity of the reported vulnerability, and then triaging the disclosure to the owners/maintainers of the affected
component(s).

We would like to begin taking steps towards creating a community process for triaging open source vulnerabilities
that is separate from the internal 2U process.

Using this opportunity, we would also like to iterate on the current process based on feedback we have heard from the
community.

Proposal
********
Having a security process does not mean that we guarantee the security of the code.  It is still provided as-is.
The work done here will be a best effort by the Open edX Community to keep our ecosystem safe and healthy.

Security Process
================

Reports come into security@openedx.org
--------------------------------------
The email will be added to SECURITY.md in Github, and an email link will be provided on openedx.org along with the Open edX
security policy.

Disclosure triage by on-call Security Working Group Member
----------------------------------------------------------
Triage responsibilities include:

* On-call person stays with security issue until it is resolved
* On-call responsibilities

  * Triage and calculate severity of issue
  * Notify and work with SME for the impacted area(s)
  * Get ticket prioritized and resolved (i.e. project manage the ticket(s) in SME backlogs)

    * Maintainers are responsible for resolving security issues
    * Security Group will set security SLA to manage expectations for time to resolve
  * Work with SMEs to release the change to master and current supported Open edX release

Because the on-call member is expected to follow a disclosure through its eventual resolution, some of these
responsibilities will extend beyond the initial scheduled on-call time.

We would also like to specifically note that if an on-call week is particularly time intensive, the on-call member can
ask the other working group members for support.

.. _security-releases:

Security Releases
-----------------
As opposed to the current security process, which involves sending a disclosure and security patch to members of the
open source security email list and waiting two days before making the patch public, are proposing the following
security release process:

#. A notice will be sent to major deployers to announce an upcoming security patch, specifying the date and time at which
   the patch will become public
#. Updates will be merged directly to master and backported to the current supported named release.
#. At the time that the update is merged to master, the update will also be announced to everyone.

Focus on proactive security improvements
========================================
Part of the work for this group should include proactive security improvements to the Open edX codebase, which could
include the below examples.

Examples
--------
#. Security suites as part of Github CI.
#. Better visibility for security supply chain issues.

   * How can we take advantage of the alerts that Github provides for security prioritization?
#. Iterations on the security process.
#. Review industry best practices that we should consider implementing.

Consequences
************

Security Working Group
======================

Invite Only
-----------
Initial members of the working group will come from the existing internal security group and members of tCRIL. If more
volunteers are needed, we will put out a call to join. Volunteers will need to ask for support from other core contributors
and will then be evaluated by the existing working group members. We hope to have six to seven members of the working
group at a time.

Security work will be a mix of private and public tickets
---------------------------------------------------------
Proactive work for security improvements will be made public, while vulnerability reports will be handled privately.
We propose using Github Security Advisories to handle the triage of vulnerability reports to the owners of
vulnerable components.

Security Team Member Responsibilities
-------------------------------------
#. Participate in the on-call rotation

   * All on-call responsibilities outlined above
#. Time dedicated towards proactive security work.
#. Participate in regular security working group meetings.
#. Keep vulnerabilities private until a coordinated disclosure occurs.

Security Backlog
================
Proactive work that will be taken on by the team will exist in a security backlog.

No more early warning via security-notifications mailing list
=============================================================
Members of this mailing list had to apply the patch to their forks of edx-platform, which are also public, so we
are not guaranteed that the patch wouldn't accidentally become public. Dealing with patches and private deployment
sources adds complexity to deployments, which can be minimized by the steps outlined in Security Releases.

Guidance for Operators
**********************
What do I do if I am an operator and someone reports a vulnerability to me?

* If you believe this is a vulnerability in the Open edX Platform, please forward the issue to security@openedx.org
  or ask the reporter to re-file the issue to security@openedx.org

Change History
**************

2022-10-24
==========

* Document created
* `Pull request #401 <https://github.com/openedx/open-edx-proposals/pull/401>`_
