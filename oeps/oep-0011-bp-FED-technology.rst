======================================
OEP-11: Front End Technology Standards
======================================

+---------------+--------------------------------------------------+
| OEP           | :doc:`OEP-11 <oep-0011-bp-FED-technology>`       |
+---------------+--------------------------------------------------+
| Title         | Front End Technology Standards                   |
+---------------+--------------------------------------------------+
| Last Modified | 2018-02-22                                       |
+---------------+--------------------------------------------------+
| Author        | Andy Armstrong <andya@edx.org>,                  |
|               | George Babey <gbabey@edx.org>                    |
+---------------+--------------------------------------------------+
| Arbiter       | Matt Drayer <mattdrayer@edx.org>                 |
+---------------+--------------------------------------------------+
| Status        | Accepted                                         |
+---------------+--------------------------------------------------+
| Type          | Best Practice                                    |
+---------------+--------------------------------------------------+
| Created       | 2016-10-19                                       |
+---------------+--------------------------------------------------+

Abstract
========

This document describes the technology standards for developing
front end applications and components for Open edX. It covers the techniques
and approaches that are currently recommended, as well as the technologies
and libraries that should be used.

Motivation
==========

The rapid pace of development in front end tooling has created opportunities
to greatly improve the quality of the Open edX user and developer experience.
After an assesment of industry best practices, the edX Front End Working Group
(FedX) has made a number of technology recommendations for libraries, frameworks
and tooling to modernize the edX front end.

Within this document, the phrase "front end" is used to mean any part
of the platform that is shown to users. This encompasses views rendered in
Python on the server, interactive interfaces written using JavaScript, and
CSS styling.

Specification
=============

Technology Selection
~~~~~~~~~~~~~~~~~~~~

.. _Use React and Redux:

1. **Use React and Redux**

   **Rationale**: React should be used for building new UIs, as it is
   widely adopted by the community and strikes a balance between
   flexibility and feature richness. For state management of complex
   client-side interactions, Redux should be used. This library was chosen
   because it sees strong use in the React community, but is also flexible
   enough to be used in situations where a hybrid React/Backbone architecture
   exists.

   When building in existing Django server-rendered pages, one can use the
   react_render helper method to bridge to React. This bridge provides an
   easy way to pass data into a React component from Django via props. For
   more information about react_render see `ReactRender`_.

   React also has a strong testing story. React components can be tested in
   isolation with unit tests using `Jest`_ and `Enzyme`_.

   Other modern frameworks considered for use
   included Polymer and Angular. See `Reasons for rejecting Angular`_  and
   `Reasons for rejecting Polymer`_ for an exploration of why these
   alternatives do not work as well as React for edX.

2. **Target the latest standardised JavaScript version (ECMA-262)**

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
   other languages that do not follow the ECMA-262 spec should not be used. See
   `Reasons for rejecting CoffeeScript`_ and
   `Reasons for rejecting TypeScript`_ for more detail.

3. **JavaScript code should follow the edX ESLint configuration**

   **Rationale**: In order to standardize and enforce Open edX's JavaScript
   coding style across multiple codebases, edX has published an ESLint
   configuration that provides an enforceable specification. The
   `edX ESLint Config`_ is made available as an npm package that can be
   installed into any Open edX package.

   **Exception**: The `edX ESLint Config for ES5`_ may be used only where ES5
   is in use. Both configs may be used in the same codebase through the
   use of `ESLint configuration cascading`_.

4. **JavaScript libraries should be installed via npm**

   **Rationale**: It is important that JavaScript libraries are kept
   up-to-date, and one key component is to make it as simple as possible
   to perform upgrades. Projects that use npm maintain all of their
   library requirements in a single `package.json` file, which can be
   easily changed as the versions change.

5. **JavaScript projects should publish a package.lock file**

   **Rationale**: To keep dependencies up-to-date and builds consistent, JavaScript
   projects should allow patch and minor upgrades in their package.json file and
   commit a package-lock.json file. The package-lock.json file will keep a full list
   of dependencies and their versions, ensuring when built for deployments the
   version of libraries are consistent. This follows the same pattern used in edX
   Python code - see `OEP 18`_ for more information. For more information on
   package-lock files see `Package Lock`_.

6. **Keep dependencies up to date by using Greenkeeper**

   **Rationale**: JavaScript dependencies are updated constantly and can be difficult
   to maintain over time. `Greenkeeper`_ makes this easier by automatically updating
   dependencies in a package.json file and verifying the tests still work. When
   possible, leverage Greenkeeper to ensure JavaScript software remains up to date.

7. **JavaScript should be bundled using Webpack**

   **Rationale**: `Webpack`_ is the tool of choice for code optimization and
   bundling, as it is widely used throughout the industry, and because
   it seamlessly handles modern code bases as well as all of the older
   technologies used by edX, such as `AMD Modules`_. Webpack should be
   implemented to handle as much of the "asset pipeline" as possible,
   rather than passing this responsibility on to Django.

8. **JavaScript dependencies should be managed with ES2015 Modules**

   **Rationale**: JavaScript module systems allow front end code to specify
   its dependencies and be grouped into bundles that minimize the assets
   needed to provide page functionality. The most prevalent module syntax
   is currently `ES2015 Modules`_, which should be adopted everywhere
   edX code is written to the ES2015 spec or later.

   **Exception**: Much of edX's existing (ES5) JavaScript uses the older
   `AMD Modules`_ syntax for modules. AMD Modules are interoperable
   with ES2015 Modules if Webpack is used for bundling, so AMD is an
   acceptable module definition if the code must remain ES5.

9. **CSS should be generated using Sass**

   **Rationale**: Sass's SCSS syntax is an extension of CSS that adds power
   and elegance to the basic language. It makes the maintenance of large
   CSS files more manageable though the ability to use variables, mixins,
   imports and more. In particular, it makes theming possible with the
   ability to override variables that define colors, fonts etc.

   You can find out more about Sass in the official `Sass documentation`_.

10. **Bootstrap for styling consistency**

  **Rationale**: In order accelerate development, and provide a more consistent
  user experience for our users, Open edX will adopt Bootstrap 4 to style its web
  applications. For more details, see :doc:`oep-0016-bp-adopt-bootstrap`.

11. **API calls should be made with the edX Frontend Auth Client or Axios**

   **Rationale**: The `edX Frontend Auth Client`_ simplifies the process of
   talking to edX APIs by using Axios inteceptors and handling JWT Cookie
   authentication. It also provides React components to handle private routes
   and should be used when possible. When making calls to non-edX APIs
   Axios should be used to provide a consistent API.

   The `fetch`_ API was considered but Axios was chosen for its more
   intuitive API, particularly when handling HTTP errors with rejected
   promises.

12. **Server-side content should be rendered with Django Templates**

   **Rationale**: Although it is advised to use client side templating with
   React, see `Use React and Redux`_,  when rendering on the server Django templates
   should be used. There are many template languages available for Django,
   but the simplest option is to use the built-in Django template engine.
   The Open edX codebase has a mixture of Django and Mako templates, but the
   former are easier to reason about because they don't support arbitrary
   code evaluation. See `Reasons for rejecting Mako`_ for a more detailed
   explanation.

   **Exception**: Mako templates can continue to be used within edx-platform
   for consistency with the existing code base. This is because the base
   templates and theming support are all provided via Mako, so it is too
   much to expect a new feature to be implemented with a different framework.
   There is much desire to replace Mako within edx-platform so this
   exception may eventually be removed.

Rejected Alternatives
=====================

.. _Reasons for rejecting Angular:

1. **Angular**

   `Angular`_ was a popular web framework in its 1.x iteration, and its
   maintainer (Google) recently released Angular 2. Angular 1 was not considered
   due to its support status. Angular 2 was rejected due to its opinionated
   characteristics including its use of TypeScript (a compile-to-JavaScript
   language) and the difficulty of incrementally integrating Angular into an
   existing application (e.g., alongside legacy Backbone). Consensus in the
   front end community is that Angular has a higher learning curve than
   alternatives like React, which matches poorly with edX's limited front end
   developers and larger community of infrequent JavaScript contributors.

.. _Reasons for rejecting Polymer:

2. **Polymer**

   `Polymer`_ is a framework built around `Web Components`_, a set of draft
   extensions to the HTML and DOM specs for UI encapsulation. Web Components
   are a very interesting area of emergent Web technology, but are not yet
   finalized as a spec or supported in many browsers. Because of this, the
   Polymer community is small and performance issues exist. As Web Components
   become more mature, edX may reevaluate their use, either with Polymer or
   without.

.. _Reasons for rejecting CoffeeScript:

3. **CoffeeScript**

   The Open edX codebase made heavy use of CoffeeScript for several years,
   but its use at edX has now been officially deprecated. Most of the language
   benefits of CoffeeScript were made available in JavaScript as of the ES2015 spec,
   and CoffeeScript's community has largely moved on.

.. _Reasons for rejecting TypeScript:

4. **TypeScript**

   Several variants of typed JavaScript have grown in community popularity
   recently, with `TypeScript`_ as the most popular. edX believes adding
   typing to our JavaScript would raise the barrier to entry for less
   experienced front end developers, and that using TypeScript in particular
   would lock us into a tool with an uncertain future (as we experienced with
   CoffeeScript). If typing is an absolute necessity for a feature
   (e.g., an API client or sensitive business logic), `Flow`_ is recommended,
   as it can be incrementally added and evaluated alongside non-typed code.

.. _Reasons for rejecting Mako:

4. **Mako**

   There are a number of reasons why Django templates is preferred to Mako:

   * Mako templates are not safe by default, meaning that it is very easy
     to introduce cross-site scripting vulnerabilities.
   * Mako templates are hard to unit test because they require that the
     whole Django context be available in case the template tries to
     access it. Django templates only have access to the context object
     that is passed to it, so it is very easy to simulate all possible
     scenarios.

.. Cross-references
.. _AMD Modules: https://github.com/amdjs/amdjs-api/wiki/AMD
.. _Angular: https://angular.io/
.. _Babel: https://babeljs.io/
.. _edX ESLint Config: https://github.com/edx/eslint-config-edx/tree/master/packages/eslint-config-edx
.. _edX ESLint Config for ES5: https://github.com/edx/eslint-config-edx/tree/master/packages/eslint-config-edx-es5
.. _edx Frontend Auth Client: https://github.com/edx/frontend-auth
.. _enzyme: https://airbnb.io/enzyme/
.. _ESLint configuration cascading: https://eslint.org/docs/user-guide/configuring/configuration-files#cascading-and-hierarchy
.. _ES2015 Modules: http://www.ecma-international.org/ecma-262/6.0/#sec-imports
.. _ES2017: https://tc39.github.io/ecma262/
.. _Fetch: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
.. _Flow: https://flow.org/
.. _Greenkeeper: https://greenkeeper.io/
.. _Jest: https://jestjs.io/
.. _JSX: https://facebook.github.io/react/docs/introducing-jsx.html
.. _oep 18: https://open-edx-proposals.readthedocs.io/en/latest/oep-0018-bp-python-dependencies.html
.. _package lock: https://docs.npmjs.com/cli/v6/configuring-npm/package-locks
.. _Polymer: https://www.polymer-project.org/
.. _React: https://github.com/facebook/react
.. _React at edX: https://openedx.atlassian.net/wiki/display/FEDX/React
.. _ReactRender: https://github.com/edx/edx-platform/blob/master/common/djangoapps/pipeline_mako/templates/static_content.html#L146
.. _Sass documentation: http://sass-lang.com/
.. _Typescript: https://www.typescriptlang.org/
.. _Webpack: https://webpack.github.io/
.. _Web Components: https://www.webcomponents.org/
