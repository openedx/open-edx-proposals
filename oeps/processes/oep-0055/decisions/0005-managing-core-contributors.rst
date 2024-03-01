0005 Managing Core Contributors Of Your Maintained Repository
#############################################################

Status
******

**Under Review**

Context
*******

Per `OEP-55
<https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0055-proc-project-maintainers.html>`_,
repository maintainers are responsible "for the health of the component and the
component's community". Further, OEP-55 specifies that maintainers must
participate in the "selection and approval of Core Contributors with commit
rights to their repositories, by participating in the comment period for Core
Contributor nominees nominated to commit code to their repo(s)".

However, OEP-55 does not provide sufficient guidance around how Maintainers and
Core Contributors should interact.

Decision
********

Maintainers must participate in, and support, the Core Contributor program.
Maintainers may not take actions that effectively prevent a Core Contributor
from being able to carry out their Rights, as defined by `OEP-54
<https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0054-core-contributors.html>`_.

Maintainers should communicate architectural principles and code standards to
Core Contributors on their repository. They may initiate the process to remove
Core Contributors that do not follow standards or respond to feedback.

Specification
*************

Maintainer Responsibilities
===========================

* Maintainers must participate in the selection of Core Contributors to their
  repository by expressing their opinion of nominees during the `Core
  Contributor nomination process <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0054-core-contributors.html#new-core-contributor-nomination-process>`_

* Maintainers must carefully weigh the merits of a nominee. Nominees may only be rejected on the basis of their contributions and conduct, not for the sake of limiting the number of contributors. For nominees that are objected to, Maintainers
  must provide feedback that includes what the nominee needs to do in order to
  become a Core Contributor on the repository.

* Maintainers may not take any action that would prevent existing Core
  Contributors from being able to merge code. This includes, but is not limited
  to, setting up branch protection rules that require a Maintainer to approve a
  pull request before it can be merged.

Maintainer Rights
=================

* Maintainers have the right to know who has Commit access on their repository,
  and communicate their code standards and architectural principles with them.
  See the `Core Contributors wiki page <https://openedx.atlassian.net/wiki/spaces/COMM/pages/3156344833/Core+Contributors+to+the+Open+edX+Project>`_
  for a list of all current and former CCs.
  New Maintainers taking over maintenance of an existing repository should
  especially ensure they are in contact with their repo's CCs. See the `maintainers how-to
  <https://docs.openedx.org/en/latest/developers/how-tos/maintain-a-repo.html#your-ccs-are-aligned-with-your-expectations-as-maintainer>`_
  for more detail.

* Maintainers have the right to remove Core Contributors who are not
  appropriately following Open edX community standards, Core Contributor
  standards (`the "3 Cs"
  <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0054-core-contributors.html#adding-new-core-contributors>`_)
  or project expectations, and have also not been responsive to Maintainer feedback.
  `OEP-54 specifies how to initiate this process <https://docs.openedx.org/projects/openedx-proposals/en/latest/processes/oep-0054-core-contributors.html#removing-core-contributors>`_.
