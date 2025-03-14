.. _Use Renovate to update dependencies:

Use Renovate to update dependencies
###################################

Status
******

Accepted

Context
*******

To ensure that the 3rd-party JavaScript packages we depend on get
updated routinely (for security patches, bug fixes, etc.), we use
a service to regularly create pull requests that update them.
These pull requests notify us that the dependency has been updated
and trigger test runs to check if the dependency can be safely
upgraded without breaking functionality.

Why we use a service like this:

* Security problems are occasionally found in packages we use,
  and we want to upgrade to versions that fix them as soon as
  possible to avoid being targeted by attacks

* It's not uncommon to spend hours debugging a problem which turns out
  to be a bug in a package which was already fixed in a newer release

* Upgrading existing dependencies in a pull request to add a feature or
  fix a bug distracts reviewers from the actual changes that had to be made

* Manually creating pull requests to upgrade dependencies (and circling
  back to check the results) is an inefficient use of developer time given
  that this is easily automated

Decision/Consequence
********************

The Open edX community uses `Renovate <https://renovatebot.com/>`_ to
automate updates for frontend dependencies.

Rejected Alternatives
*********************

1. **Greenkeeper**

   * With `Greenkeeper <https://greenkeeper.io/>`_, each package to be
     upgraded got its own pull request on every release, which was good
     for identifying the root cause of a test failure but resulted in a
     pretty high volume of PRs that needed to be reviewed (especially in
     repositories which normally don't need to be updated very often). Renovate has
     `configuration options to set the frequency of checking for new dependency
     releases <https://renovatebot.com/docs/noise-reduction/#scheduling-renovate>`_,
     and to allow related packages to be `updated in the same pull
     request <https://renovatebot.com/docs/noise-reduction/#package-grouping>`_. This may
     delay notification of security patch releases or make it harder to identify the
     exact cause of a test failure, but also makes it less likely that the PRs will be
     simply ignored for lack of time to deal with them all.

   * ``package-lock.json`` was updated with each pull request, which often caused merge
     conflicts as different PRs changed the file in slightly incompatible ways.  Some of
     the Renovate configuration options can make such conflicts less frequent, and when
     auto-merging is enabled (for specific packages when tests pass), it `automatically
     rebases <https://renovatebot.com/docs/noise-reduction/#automerging-and-scheduling>`_
     other pending pull requests.

   * Greenkeeper was `discontinued in June 2020 <https://greenkeeper.io/>`_
