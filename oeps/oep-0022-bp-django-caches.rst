===========================
OEP-0022: Caching in Django
===========================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-0022 </oeps/oep-0022-bp-django-caches>`      |
+-----------------+--------------------------------------------------------+
| Title           | Caching in Django                                      |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2018-05-21                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Robert Raposa <rraposa@edx.org>                        |
+-----------------+--------------------------------------------------------+
| Arbiter         | Alex Dusenbery <adusenbery@edx.org>                    |
+-----------------+--------------------------------------------------------+
| Status          | Under Review                                           |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2018-05-04                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | 2018-06-06                                             |
+-----------------+--------------------------------------------------------+

Context
-------

We have a variety of tools for caching in Django in various repositories,
but the solutions are not in a shared repository and don't meet all needs. A
variety of issues were uncovered while working on caching fixes and
improvements within one of our services.

1. There is a common caching bug while using Django caches where someone
   caches a value that is Falsey (e.g., None or []) and it is mistakenly
   treated as a cache miss.

2. In Django we commonly use the Memcached back-end. There are various
   places in code where we read the same value many times within a
   the scope of a request, and each of these is a network call to
   Memcached.

3. For testing and business purposes, it is sometimes useful to be able
   to invalidate a cached value on a per-request basis.

4. While refactoring caching code and reviewing potential libraries, there
   seem to be potential best practices that would hide the boilerplate
   caching logic and better highlight the business logic.

5. There is potential for better standardizing how we handle cache keys.
   For example, to generate keys that work with Memcached and avoid key
   collisions in a global space like Memcached.

Note: When and if caching is warranted in any particular circumstance is beyond
the scope of this OEP.

Recommendations
---------------

The following recommendations include patterns that are generally useful. The
intention is to better codify these recommendations into shared libraries over
time and update this documentation as the need arises.

Common Caching Defect and Fix
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following is a common defect when caching using Django caches::

    cached_value = cache.get(key)
    if not cached_value:
        # calculate value, set in cache, and return
        # if the calculated value is Falsey (e.g., None or []), it will
        # be treated as a cache miss above.
    return cached_value

The simplest fix for this defect is::

    _CACHE_MISS = object()

    cached_value = cache.get(key, _CACHE_MISS)
    if cached_value is _CACHE_MISS:
        # calculate value, set in cache, and return
        # if the calculated value is Falsey (e.g., None or []), it will
        # no longer be treated as a cache miss above.
    return cached_value

To solve this problem, use caching utility methods that avoid this defect. See
`Implementations`_ for more details.

Tiered Cache with Request Caching
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use a library that allows tiered-caching calls that use a Request Cache backed
by a slower Django cache (often Memcached). With a tiered cache, multiple
calls for the same value within a request will not require additional network
calls to Memcached.

See `Implementations`_ for more details.

Force Cache Refresh
~~~~~~~~~~~~~~~~~~~

When using any Django cache it is recommended that caching utilities offer a
simple method by which to force a cache refresh for a given request. This is
useful both for testing purposes, as well as for business purposes if a problem
occurs in Production that requires a capability for refreshing a cached value.

Note: This recommendation comes for free with a Request Cache, which is already
refreshed at the start of every request.

See `Implementations`_ for more details.

Cache Decorators
~~~~~~~~~~~~~~~~

The following is not yet a strong recommendation. More work is required to
assess and clarify.

When caching, create cache methods where the boilerplate caching code is hidden
behind a decorator. This practice allows the more important business logic to
stand out.

Note that in some cases, the boilerplate logic hidden behind the decorator
includes opinionated code regarding how keys are defined and when and if the key
will change. More assessment is required here to determine the recommended
decorator(s) to use.

Sample Before::

    def get_and_cache_my_business_value(x):
        cached_value = cache.get(key, _CACHE_MISS)
        if cached_value is _CACHE_MISS:
            new_value = calculate_my_business_value()
            key = get_my_business_key(x)
            cache.set(key, new_value)
            return new_value
        return cached_value

Sample After::

    @sample_cache_decorator(['x'])
    def get_and_cache_my_business_value(x):
        # calculate my_business_value
        ...
        return my_business_value

See `Implementations`_ for more details.

Generating Cache Keys
~~~~~~~~~~~~~~~~~~~~~

Use a utility function that will provide a safe key for your Django cache. If
you are using Memcached, the utility must avoid key conflicts in a global
space, as well as following other rules for Memcached like maximum key length.
This functionality may or may not be packaged in a caching decorator as well.

See `Implementations`_ for more details.

Implementations
~~~~~~~~~~~~~~~

This section details various implementations of the documented recommendations.
It will be updated as shared libraries are selected, moved and evolved for
better reuse in any repository.

**edx-platform repository utilities:**

+---------------------+------------------------------------------------------------------------------------------------+
| Common Defect Fix   | There is no shared utility.                                                                    |
+---------------------+------------------------------------------------------------------------------------------------+
| Tiered Cache        | There is an `edx-platform RequestCache`_, but no Tiered Cache.                                 |
+---------------------+------------------------------------------------------------------------------------------------+
| Force Cache Refresh | There are no utilities for forcing a cache refresh for the Django caches.                      |
+---------------------+------------------------------------------------------------------------------------------------+
| Cache Decorators    | There are various cache decorators. These could be assessed when considering creating a shared |
|                     | library of caching utilities.                                                                  |
+---------------------+------------------------------------------------------------------------------------------------+
| Generating Keys     | There are common utilities for generating cache keys. These should be assessed when            |
|                     | considering creating a shared library of caching utilities.                                    |
+---------------------+------------------------------------------------------------------------------------------------+

**ecommerce repository utilities:**

+---------------------+------------------------------------------------------------------------------------------------+
| Common Defect Fix   | The `RequestCache and TieredCache`_ both handle the `Common Caching Defect and Fix`_. Although |
|                     | it is simple to refactor standard Django caching code to use these classes, the interface to   |
|                     | these classes is non-standard and the simple refactor doesn't move toward using cache          |
|                     | decorators.                                                                                    |
+---------------------+------------------------------------------------------------------------------------------------+
| Tiered Cache        | There is both a `RequestCache and TieredCache`_.                                               |
+---------------------+------------------------------------------------------------------------------------------------+
| Force Cache Refresh | The TieredCache provides functionality for forcing cache refreshes.                            |
+---------------------+------------------------------------------------------------------------------------------------+
| Cache Decorators    | There is no cache decorator utility.                                                           |
+---------------------+------------------------------------------------------------------------------------------------+
| Generating Keys     | There are common utilities for generating cache keys. These should be be assessed when         |
|                     | considering creating a shared library of caching utilities.                                    |
+---------------------+------------------------------------------------------------------------------------------------+

**Quickcache library**

Note: The `quickcache library`_ has not been fully assessed. It is provided as a
good potential candidate for a shared library.

+---------------------+------------------------------------------------------------------------------------------------+
| Common Defect Fix   | The `Common Caching Defect and Fix`_ is handled by using a decorator. Any calls to the Django  |
|                     | cache outside of the decorator would be susceptible to the bug.                                |
+---------------------+------------------------------------------------------------------------------------------------+
| Tiered Cache        | Provides a TieredCache, but no RequestCache. The library would need to be enhanced to include  |
|                     | the RequestCache, or to extend to use one.                                                     |
+---------------------+------------------------------------------------------------------------------------------------+
| Force Cache Refresh | There is functionality for skipping the cache. However, this functionality still needs to be   |
|                     | assessed.                                                                                      |
+---------------------+------------------------------------------------------------------------------------------------+
| Cache Decorators    | The library is based on a slick @quickcache decorator. The decorator is opinionated about its  |
|                     | cache keys, tied to the code it is wrapping. This needs to be assessed to see if it is a good  |
|                     | fit, or can be extended for our needs, or has patterns we'd like to follow.                    |
+---------------------+------------------------------------------------------------------------------------------------+
| Generating Keys     | As written, the cache key generation is built into the decorator. This needs to be assessed.   |
+---------------------+------------------------------------------------------------------------------------------------+

**Next steps:**

* Choose the best solution(s) to the above problems and create a shared library
  (or libraries) that enable these best practices.

* Use linting utilities to enforce the best practices.

.. _edx-platform RequestCache: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/request_cache/__init__.py
.. _RequestCache and TieredCache: https://github.com/edx/ecommerce/blob/master/ecommerce/cache_utils/README.rst
.. _quickcache library: https://github.com/dimagi/quickcache

Consequences
------------

As long as we keep all options open for developers, these best practices and
supporting libraries should only help developers write cleaner and less buggy
caching code. Caching utilities are meant to make development easier, but
they do not replace the need for developers to understand when and what
caching solution is right for a given situation.

Other References
----------------

Additional references that may be useful.

* `Django's cache framework`_

.. _Django's cache framework: https://docs.djangoproject.com/en/1.11/topics/cache/
