Render server-side content with Django Templates
################################################

Status
******

Accepted

Context
*******

There are many template languages available for Django, the Open edX community must standardize on a template language.

Decision
********

When rendering on the server Django templates
must be used. There are many template languages available for Django,
but the simplest option is to use the built-in Django template engine.

Consequence
***********

The Open edX codebase has a mixture of Django and Mako templates, but the
former are easier to reason about because they don't support arbitrary
code evaluation. Moving forward, all new templates must be created using the built-in Django template engine.

Rejected Alternatives
*********************

.. _Reasons for rejecting Mako:

1. **Mako**

   There are a number of reasons why Django templates is preferred to Mako:

   * Mako templates are not safe by default, meaning that it is very easy
     to introduce cross-site scripting vulnerabilities.
   * Mako templates are hard to unit test because they require that the
     whole Django context be available in case the template tries to
     access it. Django templates only have access to the context object
     that is passed to it, so it is very easy to simulate all possible
     scenarios.
