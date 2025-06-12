Use BundleWatch to automate bundle size checking
################################################

Status
******

Accepted

Context
*******

It is important for users of the Open edX platform that we deliver reasonably sized JavaScript bundles. This provides
faster load times to all users, and is vital for users with low bandwidth and/or metered connections.

It is currently possible to manually monitor bundle size information by utilizing webpack configurations provided by
frontend-build, but this functionality is not very discoverable. Given the "hidden" nature of this functionality, it is
very possible for changes that increase bundle size to be missed in the PR review process.

The increased visibility provided by automated bundle size monitoring will ensure we don't unintentionally increase
the size of our JavaScript bundles, as well as encourage maintainers to adopt best practices such as `code splitting`_.

Decision
********

`BundleWatch`_ will be adopted by the Open edX community for automatic JavaScript bundle size monitoring. Repository maintainers will
decide if this CI check will be blocking or informative on a repo-by-repo basis.

BundleWatch
===========

`BundleWatch`_ is solely focused on ensuring bundle sizes stay under control. It is actively maintained and used by popular projects such as `bootstrap`_.

Consequence
***********

Implementing BundleWatch will require very few changes to our existing CI workflows. The BundleWatch GitHub app
will be added to the Open edX GitHub organization, and bundle size checking will be added to the CI process for
JavaScript based repositories.

Rejected Alternatives
*********************

1. **Code Checks**

   `Code Checks`_ is a pluggable framework for automated code review. It provides more
   than just bundle size monitoring, presenting us with an opportunity to rethink our
   current CI workflows. For example, Code Checks provides test coverage monitoring,
   which would allow us to re-evaluate the choice to use CodeCov as documented in
   :ref:`Use Codecov`.

   While this additional functionality is potentially desirable, moving forward with a
   single purpose tool (`BundleWatch`_) provides us the functionality we need with
   minimal changes to our current CI workflows.

2. **Bundlesize**

   `Bundlesize`_ is no longer actively maintained.

.. _Bundlesize: https://github.com/siddharthkp/bundlesize
.. _BundleWatch: https://bundlewatch.io/
.. _bootstrap: https://github.com/twbs/bootstrap/blob/main/.github/workflows/bundlewatch.yml
.. _Code Checks: https://www.codechecks.io/
.. _code splitting: https://webpack.js.org/guides/code-splitting/
