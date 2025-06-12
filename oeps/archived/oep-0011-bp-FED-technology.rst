.. _OEP-11 Front End Technology Standards:

OEP-11: Front End Technology Standards
######################################

+---------------+------------------------------------------------------+
| OEP           | :ref:`OEP-11 <OEP-11 Front End Technology Standards>`|
+---------------+------------------------------------------------------+
| Title         | Front End Technology Standards                       |
+---------------+------------------------------------------------------+
| Last Modified | 2018-02-22                                           |
+---------------+------------------------------------------------------+
| Author        | Andy Armstrong <andya@edx.org>,                      |
|               | George Babey <gbabey@edx.org>                        |
+---------------+------------------------------------------------------+
| Arbiter       | Matt Drayer <mattdrayer@edx.org>                     |
+---------------+------------------------------------------------------+
| Status        | Replaced                                             |
+---------------+------------------------------------------------------+
| Type          | Best Practice                                        |
+---------------+------------------------------------------------------+
| Created       | 2016-10-19                                           |
+---------------+------------------------------------------------------+

.. warning::

   This OEP has been replaced and may be out of date.  For the most up-to-date
   information see :ref:`OEP-67 Standard Tools and Technologies`.


Abstract
********

This document describes the technology standards for developing
front end applications and components for Open edX. It covers the techniques
and approaches that are currently recommended, as well as the technologies
and libraries that should be used.

Motivation
**********

The rapid pace of development in front end tooling has created opportunities
to greatly improve the quality of the Open edX user and developer experience.
After an assessment of industry best practices, the edX Front End Working Group
(FedX) has made a number of technology recommendations for libraries, frameworks
and tooling to modernize the edX front end.

Within this document, the phrase "front end" is used to mean any part
of the platform that is shown to users. This encompasses views rendered in
Python on the server, interactive interfaces written using JavaScript, and
CSS styling.

Specification
*************

Technology Selection
====================

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

#. **Target the latest standardized JavaScript version (ECMA-262)**

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

#. **JavaScript projects should publish a package.lock file**

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

#. **CSS should be generated using Sass**

   **Rationale**: Sass's SCSS syntax is an extension of CSS that adds power
   and elegance to the basic language. It makes the maintenance of large
   CSS files more manageable though the ability to use variables, mixins,
   imports and more. In particular, it makes theming possible with the
   ability to override variables that define colors, fonts etc.

   You can find out more about Sass in the official `Sass documentation`_.

#. **API calls should be made with the edX Frontend Auth Client or Axios**

   **Rationale**: The `edX Frontend Auth Client`_ simplifies the process of
   talking to edX APIs by using Axios interceptors and handling JWT Cookie
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

#. **Codecov should be used to measure code covered by tests**

   **Rationale**: It is important to measure the amount of code covered by our
   automated test suites. By striving for a high level of test coverage, we can
   reduce the number of bugs that can only be found via manual testing. another
   added benefit comes when we include coverage runs in the CI process, as
   contributors are automatically reminded to include tests for any new code.

   The Open edX community has standardized around `Codecov`_ as a measurement tool.

Decisions
*********

.. note::

   The decisions from this OEP have been moved to :ref:`OEP-67 Standard Tools and Technologies`.

Change History
**************

2023-05-23
==========
* Mark Enzyme as deprecated
* `Pull request #487 <https://github.com/openedx/open-edx-proposals/pull/487>`_

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
