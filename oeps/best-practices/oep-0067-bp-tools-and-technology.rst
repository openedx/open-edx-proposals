OEP-67: Standard Tools and Technologies
#######################################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-67 <oep-0067-bp-tools-and-technology>`
   * - Title
     - Tools and Technology Standards
   * - Last Modified
     - 2023-09-05
   * - Authors
     - Feanil Patel <feanil@axim.org>
   * - Arbiter
     - Braden MacDonald  <braden@opencraft.com>
   * - Status
     - Accepted
   * - Type
     - Best Practice
   * - Created
     - 2023-09-05
   * - Review Period
     - 2023-09-05 - 2023-10-10
   * - Resolution
     - * https://github.com/openedx/open-edx-proposals/pull/518
   * - References
     - :doc:`/archived/oep-0011-bp-FED-technology`

Abstract
********

The Open edX Platform is built using many first- and third-party tools and technologies.
This document covers the current recommendations for which tools and technologies should be used.

Motivation
**********

The Open edX platform has a lot of first and third party tools and technology that it uses.
The problems we're trying to solve are:

* It is difficult to know the current best practice for a given area.

* It is unclear how to go about the discussion and decision-making process to
  change a best practice.

:doc:`OEP-11 </archived/oep-0011-bp-FED-technology>` serves as a great example for the
usefulness of this kind of OEP but limits itself to be relevant only to
frontend technologies.  This OEP would supersede :doc:`OEP-11
</archived/oep-0011-bp-FED-technology>` and encompass not only front-end technology but any
technology that:

* Is considered a best practice for the Open edX Platform

* Is relevant to a large subset of the Open edX Platform

Specification
*************

.. note:: We'll fill this out before the OEP lands but leaving mostly blank for initial feedback.

General Technology Selection
============================

This technology is not specific to frontend or backend code.

#. **Codecov should be used to report code coverage by unit tests**

   **Rationale**: It is important to measure the amount of code covered by our
   automated test suites. By striving for a high level of test coverage, we can
   reduce the number of bugs that can only be found via manual testing, 
   and by using codecov to run coverage in CI,
   contributors are automatically reminded to include tests for any new code.

   The Open edX community has standardized around `Codecov`_ as a coverage reporting service.

#. **Use Github actions for CI Testing**

   **Rationale**: As a healthy open source project we need good continuous integration
   testing to prevent the introduction of obvious bugs into the platform code.  The Github Actions
   tool should be used to perform Continuous Integration (CI) testing for all Open edX repositories
   that perform any sort of software testing.

Frontend Technology Selection
=============================

.. note:: 

   The phrase “frontend” is used to mean any part of the platform that is shown to
   users. This encompasses views rendered in Python on the server, interactive
   interfaces written using JavaScript, and CSS styling.
   
.. _Use React and Redux:

#. **Use React**

   **Rationale**: React must be used for building new UIs, as it is
   widely adopted by the community and strikes a balance between
   flexibility and feature richness.

   When building in existing Django server-rendered pages, one can use the
   react_render helper method to bridge to React. This bridge provides an
   easy way to pass data into a React component from Django via props. For
   more information about react_render see `ReactRender`_.

#. **Use Redux**

   For state management of complex
   client-side interactions, Redux must be used. This library was chosen
   because it sees strong use in the React community, but is also flexible
   enough to be used in situations where a hybrid React/Backbone architecture
   exists.

#. **Use Jest and React-Testing-Library to test React components**

   We use `Jest`_ and `React-Testing-Library`_ to test React components.
   We are deprecating the `Enzyme`_ library.
   New tests must not use Enzyme, and any repositories planning to move to React 18 or newer need to
   replace Enzyme.

#. **Target the latest standardised JavaScript version (ECMA-262)**

   **Rationale**: edX JavaScript should be written consistent with the latest
   ECMA-262 specification in order to ensure future support, the largest
   community and the availability of modern features. Currently, the ECMA-262
   edition 6 (ES6) is the edition with the largest support and should be used.
   To support this syntax in older browsers, use `Babel`_. Babel may also be
   configured to add syntax extensions widely adopted by the community of our
   recommended framework (e.g., `JSX`_).

   **Exception**: Much of edX's existing front end code is written conformant
   to the edition of ECMA-262 released in 2009 (ES5). Files written in ES5
   should be gradually converted to the newer standard as new development in
   those feature areas requires.

   **Note**: edX previously used CoffeeScript, but its use has now been
   deprecated. Community interest in TypeScript has also grown, but it and
   other languages that do not follow the ECMA-262 spec should not be used.

#. **JavaScript code should follow the edX ESLint configuration**

   **Rationale**: In order to standardize and enforce Open edX's JavaScript
   coding style across multiple codebases, edX has published an ESLint
   configuration that provides an enforceable specification. The
   `edX ESLint Config`_ is made available as an npm package that can be
   installed into any Open edX package.

   **Exception**: The `edX ESLint Config for ES5`_ may be used only where ES5
   is in use. Both configs may be used in the same codebase through the
   use of `ESLint configuration cascading`_.

#. **JavaScript libraries should be installed via npm**

   **Rationale**: It is important that JavaScript libraries are kept
   up-to-date, and one key component is to make it as simple as possible
   to perform upgrades. Projects that use npm maintain all of their
   library requirements in a single `package.json` file, which can be
   easily changed as the versions change.

#. **JavaScript projects should publish a package-lock file**

   **Rationale**: To keep dependencies up-to-date and builds consistent, JavaScript
   projects should allow patch and minor upgrades in their package.json file and
   commit a package-lock.json file. The package-lock.json file will keep a full list
   of dependencies and their versions, ensuring when built for deployments the
   version of libraries are consistent. This follows the same pattern used in edX
   Python code - see `OEP 18`_ for more information. For more information on
   package-lock files see `Package Lock`_.

#. **Keep dependencies up to date by using Greenkeeper**

   **Rationale**: JavaScript dependencies are updated constantly and can be difficult
   to maintain over time. `Greenkeeper`_ makes this easier by automatically updating
   dependencies in a package.json file and verifying the tests still work. When
   possible, leverage Greenkeeper to ensure JavaScript software remains up to date.

#. **JavaScript should be bundled using Webpack**

   **Rationale**: `Webpack`_ is the tool of choice for code optimization and
   bundling, as it is widely used throughout the industry, and because
   it seamlessly handles modern code bases as well as all of the older
   technologies used by edX, such as `AMD Modules`_. Webpack should be
   implemented to handle as much of the "asset pipeline" as possible,
   rather than passing this responsibility on to Django.

#. **JavaScript dependencies should be managed with ES2015 Modules**

   **Rationale**: JavaScript module systems allow front end code to specify
   its dependencies and be grouped into bundles that minimize the assets
   needed to provide page functionality. The most prevalent module syntax
   is currently `ES2015 Modules`_, which should be adopted everywhere
   edX code is written to the ES2015 spec or later.

   **Exception**: Much of edX's existing (ES5) JavaScript uses the older
   `AMD Modules`_ syntax for modules. AMD Modules are interoperable
   with ES2015 Modules if Webpack is used for bundling, so AMD is an
   acceptable module definition if the code must remain ES5.

#. **CSS should be generated using SCSS**

   **Rationale**: Sass's SCSS syntax is an extension of CSS that adds power
   and elegance to the basic language. It makes the maintenance of large
   CSS files more manageable though the ability to use variables, mixins,
   imports and more. In particular, it makes theming possible with the
   ability to override variables that define colors, fonts etc.

   You can find out more about Sass in the official `Sass documentation`_.

#. **API calls should be made with the edX Frontend Auth Client or Axios**

   **Rationale**: The `edX Frontend Auth Client`_ simplifies the process of
   talking to edX APIs by using Axios inteceptors and handling JWT Cookie
   authentication. It also provides React components to handle private routes
   and should be used when possible. When making calls to non-edX APIs
   Axios should be used to provide a consistent API.

   The `fetch`_ API was considered but Axios was chosen for its more
   intuitive API, particularly when handling HTTP errors with rejected
   promises.

#. **Server-side content should be rendered with Django Templates**

   **Rationale**: Although it is advised to use client side templating with
   React, see `Use React and Redux`_,  when rendering on the server Django templates
   should be used. There are many template languages available for Django,
   but the simplest option is to use the built-in Django template engine.
   The Open edX codebase has a mixture of Django and Mako templates, but the
   former are easier to reason about because they don't support arbitrary
   code evaluation.

   **Exception**: Mako templates can continue to be used within edx-platform
   for consistency with the existing code base. This is because the base
   templates and theming support are all provided via Mako, so it is too
   much to expect a new feature to be implemented with a different framework.
   There is much desire to replace Mako within edx-platform so this
   exception may eventually be removed.


Backend Technology Selection
============================

This section will cover any technology or tools that are not considered a part
of the frontend.

.. This section will be empty to begin with and I'll create a separate PR to add any
   technologies that we consider standard today. This will probably include:

    * Django

    * Django Rest Framework

    * Python?

    * drf-yasg (A thing that is currently standard but that I hope to change)

    * tox

    * pytest

    * edx-lint/pylint



Decisions
*********

This section lists the decision records for any time a standard technology has
been change in the system along with the reasons why and the alternatives
considered.

.. We'll have a toctree to an ADR folder with decisions.  We'll move all the existing fronted decisions to under this OEP.

.. toctree::
   :caption: Overall Decisions
   :maxdepth: 1
   :glob:

..   oep-0067/decisions/overall/*

.. toctree::
   :caption: Frontend Decisions
   :maxdepth: 1
   :glob:
   :numbered:

   oep-0067/decisions/frontend/*

.. toctree::
   :caption: Backend Decisions
   :maxdepth: 1
   :glob:

..   oep-0067/decisions/backend/*



Consequences
************

* :doc:`OEP-11 </archived/oep-0011-bp-FED-technology>` will be superseded.

* All ADRs under OEP-11 will be moved to be under this OEP instead.

* Future decisions for technology changes will require an ADR and an update to this OEP

* The tech radar will serve as a snapshot of the current state of those decisions and to simplify
  making changes, we will co-locate it with the supporting documents for this OEP.

Change History
**************

2023-09-05
==========

* Document created
* `Pull request #518 <https://github.com/openedx/open-edx-proposals/pull/518>`_

.. Cross-references
.. _AMD Modules: https://github.com/amdjs/amdjs-api/wiki/AMD
.. _Babel: https://babeljs.io/
.. _Codecov: https://about.codecov.io/
.. _edX ESLint Config: https://github.com/edx/eslint-config-edx/tree/master/packages/eslint-config-edx
.. _edX ESLint Config for ES5: https://github.com/edx/eslint-config-edx/tree/master/packages/eslint-config-edx-es5
.. _edx Frontend Auth Client: https://github.com/edx/frontend-auth
.. _enzyme: https://airbnb.io/enzyme/
.. _enzyme-adapter-react-17: https://www.npmjs.com/package/@wojtekmaj/enzyme-adapter-react-17
.. _ESLint configuration cascading: https://eslint.org/docs/user-guide/configuring/configuration-files#cascading-and-hierarchy
.. _ES2015 Modules: http://www.ecma-international.org/ecma-262/6.0/#sec-imports
.. _ES2017: https://tc39.github.io/ecma262/
.. _Fetch: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
.. _Greenkeeper: https://greenkeeper.io/
.. _Jest: https://jestjs.io/
.. _JSX: https://facebook.github.io/react/docs/introducing-jsx.html
.. _oep 18: https://open-edx-proposals.readthedocs.io/en/latest/oep-0018-bp-python-dependencies.html
.. _package lock: https://docs.npmjs.com/cli/v6/configuring-npm/package-locks
.. _React: https://github.com/facebook/react
.. _React at edX: https://openedx.atlassian.net/wiki/display/FEDX/React
.. _React-Testing-Library: https://testing-library.com/docs/react-testing-library/intro
.. _ReactRender: https://github.com/openedx/edx-platform/blob/4b38b1f750918ff83c02cff776681aabe44bd689/common/djangoapps/pipeline_mako/templates/static_content.html#L159-L167
.. _Sass documentation: http://sass-lang.com/
.. _Webpack: https://webpack.github.io/
