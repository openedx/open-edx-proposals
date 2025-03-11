.. _Use Caret Ranges for npm Dependency Versions:

Use Caret Ranges for npm Dependency Versions
############################################

Status
******

Proposed (February 2025)

Context
*******

Managing dependencies in JavaScript projects requires balancing stability, maintainability, and performance. Historically, most projects
have opted to pin exact dependency versions to ensure consistency. However, this approach has notable downsides, particularly when
considering transitive dependencies, package duplication, and update workflows.

Decision
********

JavaScript projects in the Open edX community should define dependencies using caret ranges (e.g., ``^1.2.3``) instead of pinning exact
versions (e.g., ``1.2.3``).

Why?

#. **Pinned Dependencies Do Not Fully Lock Transitive Dependencies**: Even if a top-level dependency is pinned, its transitive dependencies
   are not necessarily locked. This means updates can still introduce changes, making pinning less effective than intended.

#. **Reduces Webpack Bundle Size and Dependency Fragmentation**: When different packages pin slightly different versions of the same dependency,
   multiple versions of that dependency may be included in the final bundle. Using caret ranges allows different packages to share a single version,
   reducing duplication and improving performance.

#. **More Clearly Communicates Engineering Intent**: Specifying caret ranges explicitly indicates that a project intends to accept non-breaking
   updates in accordance with semantic versioning. This improves clarity for maintainers and contributors.

#. **Improves Renovate Workflow and Reduces Maintenance Overhead**: The Open edX platform to automate and manage dependency updates (see 
   :ref:`Use Renovate to update dependencies` for more details). With caret ranges, Renovate still detects and opens PRs for  minor and patch updates,
   but these updates typically require only a ``package-lock.json`` change rather than modifying  ``package.json``. This allows Renovate to group updates
   more efficiently and reduces unnecessary PR churn. In contrast, pinned dependencies require Renovate to open a PR for every minor or patch update to
   explicitly modify ``package.json``, increasing maintenance overhead.

Consequence
***********

#. Projects will automatically receive non-breaking dependency updates within the specified range, reducing the need for manual intervention.
#. Webpack bundle sizes may decrease due to fewer redundant versions of dependencies.
#. Renovate will generate fewer update PRs, focusing primarily on major version changes that require attention.
#. Developers should continue using lockfiles (``package-lock.json``) to ensure consistent installations across environments.

Rejected Alternatives
*********************

#. **Continuing to pin dependencies in JavaScript projects**

   * Pinning dependencies does not effectively prevent updates to transitive dependencies.
   * Leads to unnecessary duplication of packages in Webpack bundles, increasing size and complexity.
   * Results in excessive Renovate PRs for minor and patch updates, increasing maintenance burden.
