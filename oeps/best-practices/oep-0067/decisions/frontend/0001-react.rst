Use React
#########

Status
******

Accepted

Context
*******

The Open edX community needs a standardized framework for building new UIs that is
widely adopted, flexible, and feature rich.

Decision
********

React must be used for building new UIs, as it is
widely adopted by the community and strikes a balance between
flexibility and feature richness.

Consequence
***********

React has been adopted by the community as the framework to use for building new UIs.

Rejected Alternatives
*********************

Other modern frameworks considered for use included Polymer and Angular.

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

.. _Angular: https://angular.io/
.. _Polymer: https://www.polymer-project.org/
.. _Web Components: https://www.webcomponents.org/