Target the latest standardised JavaScript version
#################################################

Status
******

Accepted

Context
*******

The Open edX community must write JavaScript in a way that ensures future support and the availability of modern features.

Decision
********

The Open edX community must write JavaScript consistent with the latest
ECMA-262 specification in order to ensure future support, the largest
community, and the availability of modern features.

Consequence
***********

Not all browsers support the latest JavaScript features and syntax. To support this syntax in older browsers, the Open edX community must use `Babel`_.

Rejected Alternatives
*********************

.. _Reasons for rejecting CoffeeScript:

1. **CoffeeScript**

   The Open edX codebase made heavy use of CoffeeScript for several years,
   but its use at edX has now been officially deprecated. Most of the language
   benefits of CoffeeScript were made available in JavaScript as of the ES2015 spec,
   and CoffeeScript's community has largely moved on.

.. _Reasons for rejecting TypeScript:

2. **TypeScript**

   Several variants of typed JavaScript have grown in community popularity
   recently, with `TypeScript`_ as the most popular. edX believes adding
   typing to our JavaScript would raise the barrier to entry for less
   experienced front end developers, and that using TypeScript in particular
   would lock us into a tool with an uncertain future (as we experienced with
   CoffeeScript). If typing is an absolute necessity for a feature
   (e.g., an API client or sensitive business logic), `Flow`_ is recommended,
   as it can be incrementally added and evaluated alongside non-typed code.

.. _Flow: https://flow.org/
.. _Typescript: https://www.typescriptlang.org/
.. _Babel: https://babeljs.io/
