====================
OEP-34: Lint Amnesty
====================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-34 </oeps/oep-0034-bp-lint-amnesty>`         |
+-----------------+--------------------------------------------------------+
| Title           | Lint Amnesty                                           |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2019-03-12                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Calen Pennington <cale@edx.org>                        |
+-----------------+--------------------------------------------------------+
| Arbiter         | Usama Sadiq <usadiq@edx.org>                           |
+-----------------+--------------------------------------------------------+
| Status          | Draft                                                  |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2019-03-12                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | 2019-03-12 - 2021-04-15                                |
+-----------------+--------------------------------------------------------+

Context
-------

All software has bugs. Linting can identify a subset of those bugs (and
stylistic issues) statically before the code is merged to master or
released to production. At edX, we strive to keep violations minimal or
at zero, but for certain codebases, such as edx-platform, we have a large
backlog of violations. We use a cap to limit adding new violations, but
it must be updated manually, and so it only incrementally forces the
number of violations down. We also disallow new code changes that introduce
violations *in the lines that were changed*

Certain violations are dependent on a context larger than just the line
that the warning is on. For instance, a code change might delete a class
that is imported in a number of other code files. Those imports would now
be in violation, but they were not changed, and therefor would not be
flagged by differential linting.

Decision
--------

In all repositories, we will grant amnesty to all existing linting violations.
This includes pylint, xsslint, pycodestyle, and any other linting programs
currently in use. Lints being granted amnesty will be marked on a
per-line basis in order to disable them (in whatever way is appropriate
for the relevant linting tool). It will also be marked with an inline comment
``lint-amnesty``. For example, when granting amnesty to a pylint violation,
it would look like ``# lint-amnesty, pylint: disable=my-error``.

Once all lints have been granted amnesty, CI will be used to ensure that
no new violations are added. If a linter is upgraded or a new rule is
added, then all new violations will either be fixed or granted amnesty
as well.

Consequences
------------

Once all violations have been granted amnesty, we can enforce and maintain
a level of 0 new violations added. Because the linter would be running
across the entire codebase, and the violation limit is globally zero, a
change that introduces non-local violations would still be flagged in CI
(unlike in the differential linting approach that we currently use).

Adding the amnesty markers on every affected line will also make it clearer
to developers working inside the codebase where they can change code to
lower violations.

References
----------

There is a `prototype tool`_ for granting lint amnesty in `edx/edx-lint`_.

.. _prototype tool: https://github.com/edx/edx-lint/blob/master/edx_lint/cmd/amnesty.py
.. _`edx/edx-lint`: https://github.com/edx/edx-lint
