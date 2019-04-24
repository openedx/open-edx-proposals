========================================
OEP-0035: REST API Pagination Guidelines
========================================

+-----------------+-------------------------------------------------------------+
| OEP             | :doc:`OEP-0035 </oeps/oep-0035-bp-rest-api-pagination.rst>` |
+-----------------+-------------------------------------------------------------+
| Title           | REST API Pagination Guidelines                              |
+-----------------+-------------------------------------------------------------+
| Last Modified   | 2019-04-21                                                  |
+-----------------+-------------------------------------------------------------+
| Authors         | David Ormsbee <dave@edx.org>                                |
+-----------------+-------------------------------------------------------------+
| Arbiter         | ?                                                           |
+-----------------+-------------------------------------------------------------+
| Status          | Draft                                                       |
+-----------------+-------------------------------------------------------------+
| Type            | Best Practice                                               |
+-----------------+-------------------------------------------------------------+
| Created         | 2019-04-21                                                  |
+-----------------+-------------------------------------------------------------+
| `Review Period` | ?                                                           |
+-----------------+-------------------------------------------------------------+

Context
-------

Pagination for REST APIs is the practice of restricting list calls to return
only a small subset of the data (e.g. twenty at a time), with pointers to the
next set of results. The page to be returned is usually specified by a
querystring parameter.

Over the years, we've encountered a number of non-obvious performance issues
around pagination at edX. The goal of this OEP is to provide a simple set of
best practices that will allow us to avoid these problems, while at the same
time making our APIs more consistent.

This OEP is intended to provide general guidelines, not a absolute rules. Your
API may have special requirements that require different tradeoffs. But if you
find yourself deviating from these guidelines, please reach out to the
Architecture team to talk over why.

Pagination Overview
-------------------

We paginate APIs in order to limit the CPU, memory, disk, and network resources
required to process a request, thereby ensuring that we can serve responses
quickly and reliably to the user. This may seem obvious, but pagination is so
often taken for granted that we don't stop to think through the implications.

The fundamental question that should guide all our pagination decisions is:
**Is the work we have to do to serve any given set of requests bounded?**
Sometimes limiting the cost of a request can be done without paging. If we're
always paying the full computational cost of the entire collection on every
paged request, pagination can actually make things substantially worse.

Known Performance Issues
------------------------

What follows is an examination of common performance issues we've encountered
using pagination. It is *not* a rejection of pagination. **To be absolutely
clear: Pagination of any form is almost always better than simply letting
clients request an unbounded list.**

n + 1 Queries
+++++++++++++

The "n+1 queries" phenomenon is where each record in the list makes a separate
database request to get some related field information (because Django allows
lazy loading of related models). This is usually easy to catch in development by
looking at the number of queries in the Django Debug Toolbar.

Expensive Counts
++++++++++++++++

Returning the ``count()`` of all records that could be paged through becomes
expensive in and of itself. Counting millions of records can take seconds just
by itself. This is true even if the current page you're returning only has ten
results in it.

Fish Tailing
++++++++++++

You will often see APIs where requests to pages get more and more expensive as
you traverse the result set. When doing numbered pages, the database typically
does a query like ``LIMIT {page_size} OFFSET {records to start of current
page}``. But each time it does that, the database has to examine all the records
leading up to the ones being displayed. Meaning that if you show 100 records
starting at an offset of 1,000,000, then the database has to examine 1,000,100
rows.

Incorrect Indexes
+++++++++++++++++

As APIs become more complicated with nested relationships and sorting options,
it's possible to accidentally introduce queries that do some combination of
table scans and file sorting, either one of which will cripple performance and
threaten system stability.

Too Much Prefetch-Related
+++++++++++++++++++++++++

Django's `prefetch_related` is an important tool for avoiding "n+1" style
queries, and is the right thing to do most of the time. However, highly
normalized schemas with many nested relations in a single view may cause CPU
issues because of how `prefetch_related` is implemented by Django. This has been
an issue with the course discovery service.


Recommendations by Use Case
---------------------------

This OEP is going to examine pagination over three common listing types.

Use Case 1: Unbounded Lists of Resources for Human Interaction
==============================================================

This is the most common case for pagination, where the pages returned by an API
correspond to the UI displayed to an end user's browser. The total, unfiltered
listing of resources may be millions or billions of rows from the database, but
the user only needs to see a small fraction of that.

Recommendations
+++++++++++++++

Use Django Rest Framework's default ``PageNumberPagination`` class. It will give
the following general template::

  {
    "count": 1023
    "next": "https://api.example.org/accounts/?page=5",
    "previous": "https://api.example.org/accounts/?page=3",
    "results": [
       …
    ]
  }

The query parameter for the page number is ``page``.

Your API should accept a ``page_size`` parameter, by defining
``page_size_query_param``. However if you allow clients to specify the desired
page size, your *must* specify a ``max_page_size``.

Finally, you *must* limit the number of items in your result set. You can do
this by modifying your view's ``queryset``. For example::

  class UserViewSet(viewsets.ModelViewSet):
      serializer_class = UserSerializer
      queryset = User.objects.all().limit(1000)  # <-- Limit is here

!!!!!!!!!!!!!!!!!!!!!!!!!!!!! < add more here on explain and query plans

Use Case 2: Unbounded Lists of Resources for Script Consumption
===============================================================

Sometimes our API allows for incrementally paging through millions of records in
a way that absolutely no human would ever do. In this case, we're basically
building for scripts that are going to crawl our data. It doesn't matter what
our *intentions* are. The moment we make an API that can be crawled in this
manner, we're basically accepting that it *will* be crawled in this manner. But
those scripts have a different set of requirements and usages patterns from our
end users, in ways that complicate pagination.

1. Consistency is more important than Recency.

Scripts will generally prefer to get consistent data over extremely recent data.
If a script is going to take an hour to generate a report from your API anyway,
it probably doesn't care if data is another hour out of date. But it probably
cares very much that it's not missing any records, and would probably prefer not
to get duplicate records—both of which will almost certainly happen if we're
naviely paging through thousands of pages of records.

One of the major problems with using pagination for this sort of reporting is
that we mentally think of pages as being different parts of the same result set,
when they are actually parts of different result sets that are gradually
changing over time. For instance, say we have twenty items spread out over two
pages of ten items each, and we specify that using simple numbering (i.e.
``/?page=1``) Sometime after I read page one, but before I read page two, an
entry from page one is deleted. When I go to read page two, there are now nine
items. But there's one item that I've never read at all, because it used to be
the first item for page two, but is now the last item for page one.

2. Load Spikes

It's common to see scripts run on a daily basis, during which your site will be
bombarded with many thousands of requests, signficantly increasing load on your
system for some period of time in ways that you have limited control over. You
can take a rate limit hammer to things at the nginx layer to protect your app,
but the fact that Open edX doesn't have a good way to specify how expensive a
request is makes rate limiting a crude mechanism. Creating an entry in a
tracking log costs almost nothing, while querying the modulestore might take
seconds to execute depending on the query, but both are counted equally by rate
limiting.

3. Quickly Written and Forgotten

Scripts like this are often the least interesting part of whatever their authors
are working on. In practice, that often means they're written in a hurry and
don't really work through consistency related edge cases or thoroughly test
rate-limit backoff behavior. This can make scripts prone to subtle bugs and
misbehavior (e.g. retrying forever). If those requests are expensive, it can
cause significant load on the system and has led to system outages in the past.

Recommendations
+++++++++++++++

If your API needs to deliver many thousands of records to an automated client,
don't do this over a paginated REST API at all. Use the
[django-user-tasks](https://django-user-tasks.readthedocs.io/en/latest/) library
to asynchronously generate a report instead. This makes system performance much
more stable and controllable.

When possible, use the read replica database for your queries. You can do this
with the ``use_read_replica_if_available`` function::

    from util.query import use_read_replica_if_available

    enrollments_in_course = use_read_replica_if_available(
        CourseEnrollment.objects.filter(**filter_kwargs)
    )

Use Case 3: Strictly Bounded Lists of Resources
===============================================

Sometimes you have a list of resources where the absolute number of resources is
already strictly bounded. For example, Blockstore has a notion of files in a
Bundle. It stores the metadata describing all of those files in a single JSON
document. If we establish a limit of 2000 files per Bundle, then the maximum
output of that API is approximately 200K of uncompressed JSON data for a single
request. Furthermore, because that metadata is stored in a single JSON file, we
incur most of the cost of processing the entire collection on every request,
regardless of what page we're serving.

Some indications that this situation might apply are:

1. The length of the list returned by any given API request is bounded and *that
   limit is enforced in code*. It is not sufficient to say, "There's no reason
   it should get larger than this." This has bitten us many times. There must be
   a strict limit, with product agreement.
2. The largest possible payload does not exceed 100K compressed.
3. The largest possible payload can be served in < 100ms of server time.

Recommendations
+++++++++++++++

Don't use pagination at all, or use similar guidelines to Use Case 1.

TODO: More here.


Consequences
------------


References
----------

