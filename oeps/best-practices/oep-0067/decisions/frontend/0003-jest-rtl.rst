Use Jest and React-Testing-Library to test React components
###########################################################

Status
******

Accepted

Context
*******

The Open edX community needs consistent tooling for testing React components.

Decision
********

The Open edX community must use `Jest`_ and `React-Testing-Library`_ to test React components.
We are deprecating the `Enzyme`_ library.
New tests must not use Enzyme, and any repositories planning to move to React 18 or newer need to
replace Enzyme.


Consequence
***********

The Open edX community must use Jest and React-Testing-Library to test React components.

Rejected Alternatives
*********************

.. _Reasons for deprecating Enzyme:

1. **Enzyme**

   * `Enzyme is dead`_.
      * Enzyme is not considered to be actively maintained anymore.
      * It does not support React hooks.
      * It does not and will not support React 18.
      * The community is unwilling to create an adapter for React 18.

.. _Enzyme is dead: https://dev.to/wojtekmaj/enzyme-is-dead-now-what-ekl
.. _Jest: https://jestjs.io/
.. _React-Testing-Library: https://testing-library.com/docs/react-testing-library/intro
.. _enzyme: https://airbnb.io/enzyme/
