.. _Use React Query:

Use React Query
###############

Status
******

Accepted

Context
*******

The Open edX community needs a library to handle the common pattern of loading
data from the platform's REST APIs, as well as mutating data (submitting changes
using the API). Doing this properly and consistently requires consideration of
loading states, error handling, caching, cache invalidation, pagination, and
more.

A popular library for this purpose is
`React Query <https://tanstack.com/query/latest/docs/framework/react/overview>`_.

React Query provides a lot of useful functionality, including but not limited
to: consistent handling of loading and error states, automatic updates, caching,
cache invalidation, pagination, infinite scroll, request de-duplication,
optimistic updates, pre-fetching, compatibility with server side rendering, and
more. It does all this with a simple API, and requires a lot less boilerplate
than implementing the equivalent functionality using Redux [Toolkit].

Decision
********

For loading data from the platform or other external sources (e.g. via REST
APIs), `React Query <https://tanstack.com/query/latest/docs/framework/react/overview>`_
should be used.

For client-side state management, regular React "state" and "context" should be
used alongside React Query, following the best practices described in the
official React docs: `Managing State <https://react.dev/learn/managing-state>`_.

Consequence
***********

React Query has been adopted by the Open edX community as the library to use to
handle data loading, caching, and mutation in React applications.

Rejected Alternatives
*********************

1. **Redux**
  
   Redux was previously recommended. At the time the original OEP was
   written, React itself had poor state management capabilities and React Query
   did not yet exist; the React community in general frequently recommended
   Redux. However, now React itself has stronger state management (e.g. context)
   and for data loading, React Query provides a lot more functionality and
   simplicity compared to Redux.
