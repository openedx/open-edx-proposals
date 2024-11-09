Use TypeScript for static type checking
#######################################

Status
******

Accepted

Context
*******

As documented in :doc:`OEP-0011-0004 <0004-js-ecma>`, the Open edX community has previously
:ref:`decided against adopting TypeScript <Reasons for rejecting TypeScript>`. In the time
since that decision was made, TypeScript has grown to be quite popular and commonplace for JavaScript developers.
This has led to more discussion within the community about the `pros and cons of adopting TypeScript`_.

Decision
********

Considering the benefits of TypeScript, such as the ability to ensure proper API usage
through type definitions and to catch bugs at build time, the Open edX community has decided
to incorporate TypeScript into frontend repositories.

Frontend repositories within the Open edX project will be updated to support the use of TypeScript, without
requiring existing frontend JavaScript code to be rewritten in TypeScript.

Consequence
***********

TypeScript will be utilized in portions of frontend repositories where it can provide the most value.

Rejected Alternatives
*********************

1. **Continue without TypeScript**

   It is possible to use other tools to add some of the functionality TypeScript provides
   to JavaScript code. However, considering the main point of opposition to TypeScript adoption
   was keeping a low barrier of entry for frontend developers, this could be counterproductive.

2. **Rewrite Open edX JavaScript in TypeScript**

   It would be possible to convert all JavaScript throughout the Open edX project (or any given
   repository within the project) to TypeScript. It is not clear what the benefit of doing so would
   be.

.. _pros and cons of adopting TypeScript: https://github.com/openedx/paragon/discussions/1186
