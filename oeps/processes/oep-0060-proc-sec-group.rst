OEP-60 Open Source Security Working Group
#########################################

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-60 <oep-0060-proc-sec-group>`                |
+-----------------+--------------------------------------------------------+
| Title           | Open Source Security Working Group                     |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2022-12-02                                             |
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
| Review Period   | 2022-11-30 - 2023-01-02                                |
+-----------------+--------------------------------------------------------+

Context
*******
edX/2U has a volunteer Security Working Group (SWG) that has historically handled the triage process for any open
source vulnerabilities. This process included digesting emails that were sent to security@edx.org, assessing the
severity of the reported vulnerability, and then triaging the disclosure to the owners/maintainers of the affected
component(s).

We would like to begin taking steps towards creating a community process for triaging open source vulnerabilities
that is separate from the internal 2U process.

Using this opportunity, we would also like to iterate on the current process based on feedback we have heard from the
community.

Proposal
********
.. note::

   Having a security process does not mean that we guarantee the security of the code.  It is still provided as-is.
   The work done here will be a best effort by the Open edX Community to keep our ecosystem safe and healthy.

Security Process
================

Reports come into security@openedx.org
--------------------------------------
The email will be added to SECURITY.md in GitHub, and an email link will be provided on openedx.org along with the Open edX
security policy.

Issue Triage and Routing
------------------------
The on-call Security Working Group member will triage the incoming report.

They will:

#. Verify the report.
#. Reply to the reporter.
#. Forward operator-specific disclosures to the relevant Open edX operator.
#. Identify the affected Open edX repositories.
#. Score the severity of the vulnerability.
#. Open a GitHub security advisory in relevant repositories.
#. Notify the maintainers of the vulnerable repositories.

SWG Responsibility
------------------
The Security Working Group is responsible for advising on security and providing security-scoring-as-a-service for maintainers.

Maintainers are welcomed to email security@openedx.org anytime they need security advice. The Security Working Group will do their best to advise.

The Security Working Group will also score incoming vulnerability disclosures using the `Common Vulnerability Scoring System (CVSS), version 3.1`_. This system provides a way to score vulernabilities from 0.0 to 10.0, which map to a severity of Low, Medium, High, or Critical.

The Security Working Group will provide this numerical score and qualitative severity rating when notifying maintainers. This is so maintainers are better informed on how severe the vulnerability is and have a sense of how important it may be to interrupt their work on the repository to resolve the vulnerability.

.. _Common Vulnerability Scoring System (CVSS), version 3.1: https://www.first.org/cvss/v3.1/specification-document

Maintainer Responsibility
-------------------------
Maintainers are ultimately responsible for resolving disclosed security vulnerabilities.

To assist the maintainer with tracking their repository's vulnerabilities, the Security Working Group will create a `GitHub repository security advisory`_ pre-assigned with the scored severity.

Maintainers should inform the Security Working Group if they judge a vulnerability to be a different severity than what was originally triaged. The Security Working Group must accept the maintainer's adjudication, but should comment on any considerations around the adjudication.

Maintainers will be routinely reminded to remediate disclosures in a frequency proportional to the severity of the disclosure. The following table shows the default reminder frequency until resolution of each severity classification:

========    =====   ==================
Severity    Score   Reminder frequency
========    =====   ==================
Low         ≥0.1    Twice a year
Medium      ≥4.0    Once a quarter
High        ≥7.0    Once a month
Critical    ≥9.0    Once a week
========    =====   ==================

The on-call Security Working Group member is responsible for sending reminders for any vulnerability reminders due during the time they are on-call. These routine reminders should be batched so that all reminders of the same frequency for a maintainer are compiled into a single notification.

.. note::

    Maintainers are encouraged to reach out to security@openedx.org if they need any help remediating security issues.

.. _GitHub repository security advisory: https://docs.github.com/en/code-security/security-advisories/repository-security-advisories/creating-a-repository-security-advisory

Security Vulnerability Privacy
------------------------------
Details of security vulnerabilities will remain private until a fix is released. Members of the Open edX community with access to the details of a disclosure are asked to keep those details private until a fix is publicly released for the vulnerability.

Maintainers and their delegates should use GitHub's `temporary private fork`_ feature within the GitHub repository security advisory created by the Security Working Group to keep the implementation details of a fix for a vulnerability private until the appropriate time to release the fix to the public.

.. _temporary private fork: https://docs.github.com/en/code-security/security-advisories/repository-security-advisories/collaborating-in-a-temporary-private-fork-to-resolve-a-repository-security-vulnerability

.. _security-releases:

Security Releases
-----------------
The current process for releasing security fixes involves sending a disclosure and security patch to members of the
open source security email list and waiting two days before making the patch public.  Instead of this process, we
propose the following security release process:

#. The maintainer will create an announcement post in the `Security Announcement Section`_ on https://discuss.openedx.org. It should specify the affected repository, the date and time at which the patch will become public, and the severity of the vulnerability it fixes.

#. The maintainer will merge the fix to the repository's main branch and backport the fix to the current supported named releases around the date and time specified by the post.

#. The maintainer will `publish the GitHub security advisory`_.

#. The maintainer will add a reply to the announcement post linking to:

   * The published GitHub security advisory.
   * The pull requests that merged the fix to master.
   * The pull requests that merged the fix to the relevant supported release branches.

.. _Security Announcement Section: https://discuss.openedx.org/c/announcements/security/19
.. _publish the GitHub security advisory: https://docs.github.com/en/code-security/security-advisories/repository-security-advisories/publishing-a-repository-security-advisory


Focus on proactive security improvements
========================================
Part of the work for this group should include proactive security improvements to the Open edX codebase, which could
include the below examples.

Examples
--------
#. Security suites as part of GitHub CI.
#. Better visibility for security supply chain issues.

   * How can we take advantage of the alerts that GitHub provides for security prioritization?
#. Iterations on the security process.
#. Review industry best practices that we should consider implementing.
#. Running an annual security survey.


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

There will also be ways to participate in the security of the Open edX platform without being a member of the working group. Maintainers and core contributors of the Open edX community interested in volunteering to implement security hotfixes for other maintainers who may not have the bandwidth to immediately address vulnerabilities are encouraged to email their interest to security@openedx.org.

Visibility
----------
Security work will be a mix of private and public tickets. Proactive work for security improvements will be made public, while vulnerability reports will be handled privately.

We propose using GitHub Security Advisories to handle the triage of vulnerability reports to the owners of vulnerable components.

Member Responsibilities
-----------------------
#. Participate in the on-call rotation

   * All on-call responsibilities outlined above
#. Dedicate time towards proactive security work.
#. Participate in regular Security Working Group meetings.
#. Keep vulnerabilities private until a coordinated disclosure occurs.


Security Backlog
================
Proactive work that will be taken on by the team will exist in a security backlog.


No more early warning via security-notifications mailing list
=============================================================
Members of this mailing list had to apply the patch to their forks of edx-platform, which are also public, so we
are not guaranteed that the patch wouldn't accidentally become public. Dealing with patches and private deployment
sources adds complexity to deployments, which can be minimized by the steps outlined in `Security Releases`_ above.

.. _Security Releases: `security-releases`_


Guidance for Operators
**********************
What do I do if I am an operator and someone reports a vulnerability to me?

* If you believe this is a vulnerability in the Open edX Platform, please forward the issue to security@openedx.org
  or ask the reporter to re-file the issue to security@openedx.org

What will happen if a report is accidentally sent to security@openedx.org for the operation of my Open edX instance?

* Please let security@openedx.org know the best email (preferably a group email, like security@company.com) to forward such reports to, along with your Open edX instance name, domain, and separate contact information for an indidual responsible for security at your organization. The Security Working Group will do their best to forward such reports to the correct organization.

How do I receive notification of the release of upcoming security patches?

* Please watch the Open edX Discourse Security Announcements topic at https://discuss.openedx.org/c/announcements/security/19. If you are logged in, the button with a bell icon on the top right corner above the topic list will let you customize how you are notified of security announcements.


Change History
**************

2022-12-02
==========
* Address:

  * Annual security survey
  * Confidentiality
  * Disagreements on severity classification (and associated SLAs)
  * Disclosure timeline
  * Follow up on remediation & on-call transition
  * Security scoring
  * Temporary private forks
  * Vulnerabilities for operators

2022-12-01
==========

* Updated the announcement plan to use discourse instead of the mailing list.
* Cosmetic Changes

2022-10-24
==========

* Document created
* `Pull request #401 <https://github.com/openedx/open-edx-proposals/pull/401>`_
