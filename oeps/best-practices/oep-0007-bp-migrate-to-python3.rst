############################
OEP-7: Migrating to Python 3
############################

+---------------+-----------------------------------------------+
| OEP           | :doc:`OEP-7 <oep-0007-bp-migrate-to-python3>` |
+---------------+-----------------------------------------------+
| Title         | Migrating to Python 3                         |
+---------------+-----------------------------------------------+
| Last-Modified | 2019-02-01                                    |
+---------------+-----------------------------------------------+
| Author        | Cliff Dyer <cdyer@edx.org>,                   |
|               | Jeremy Bowman <jbowman@edx.org>               |
+---------------+-----------------------------------------------+
| Arbiter       | Christopher Pappas <cpappas@edx.org>          |
+---------------+-----------------------------------------------+
| Status        | Accepted                                      |
+---------------+-----------------------------------------------+
| Type          | Best Practice                                 |
+---------------+-----------------------------------------------+
| Created       | 2016-08-12                                    |
+---------------+-----------------------------------------------+
| Resolution    | `open-edx-proposals#21`_                      |
+---------------+-----------------------------------------------+

.. _open-edx-proposals#21: https://github.com/edx/open-edx-proposals/pull/21#pullrequestreview-18018383

Abstract
########

With primary development of Python occurring on Python 3, and with Python 2
scheduled for end-of-life on January 1st, 2020, edX needs to plan for a transition of all of
our code to Python 3.  Open edX is a large project, spanning many applications
over even more github repositories, with even more dependencies on third party
libraries.  This document outlines how we plan to scope the problem, socialize
understanding of the differences between versions of Python, migrate our code,
and work with our Open edX community to ensure that the process is as painless
as possible, and meets the needs of our stakeholders.


Rationale
#########

    * Python 2 will only receive support from the Python core developers until January 1st, 2020.
    * Django will only support Python 2 through 1.11LTS (which is supported
      until April 2020).
    * New features are being made available on Python 3 first, and are only
      sometimes backported to Python 2.
    * New syntax extensions (``async``/``await``, ``yield from``, and the ``@``
      operator, for instance) are only being introduced in Python 3.
    * Python 3 provides better separation between text and binary data.
    * Python 3 provides better memory usage through a more thorough use of
      iterators and smart collections.
    * Major language performance work is being done on Python 3 only.
    * Almost all major libraries now support Python 3.
    * We can approach the problem now at a relaxed pace, or deal with it in a
      panic later, diverting valuable resources from other important work.
    * The longer we wait, the more code we will produce that needs to be
      migrated.
    * Using Python 2 hurts our story as a leader in the Python and open
      source communities.
    * Python 3 makes for more compelling job postings [Citation needed]


Scope
#####

There are several areas of work that need to be done

* Setting policy
* Migrating existing code
* Changing coding practice
* Deploying edx.org services with Python 3
* Deprecating Python 2 support

These areas have certain dependencies between them, e.g., we cannot deploy a
service under Python 3 until all of its code has been migrated.  However, this
should not be taken as a chronological list of tasks.  We may begin deprecating
Python 2 support on certain repositories before existing code in other
repositories has been migrated.


Choosing a Python version
*************************

All new Python projects should be written using Python 3 unless a compelling
reason (such as incompatible support libraries with no reasonable alternative
available) necessitates that we stick with Python 2.


New libraries should additionally support Python 2 using a single code-base
with Six_ as a compatibility layer if they will be used by projects that run on
Python 2.  Open source libraries we maintain that are generally useful beyond
our services should also maintain compatibility with Python 2 until support for
Python 2 is dropped by the core developers, which is scheduled to happen in
January 2020.

Deployable projects only need to support a single version of Python.  Libraries
will often need to support a range of versions.  Libraries written in Python 3
should initially support versions 3.5 and up.  Libraries that support Python 2
should only support version 2.7.  Future version deprecations are outside the
scope of this document.

The end goal is for all of our services to be deployed under Python 3 or
replaced by newer services before the January 2020 end of support.


Migrating code
**************

All new code in Python 2 codebases should be written to be compatible with
Python 3.  Six_ is a simple compatibility library that makes this easier.
If something works differently in
Python 2 and Python 3, you import six, and use functions that it provides
rather than the ambiguous code.  Rather than using Python 3's ``str`` class,
which would be interpreted as a bytestring in Python 2, you use
``six.text_type``.  Instead of calling ``d.items()`` on a dictionary, you call
``six.viewitems(d)``.  Stdlib imports that have changed can be imported from
``six.moves.*``.  The compatibility layer is intrusive throughout the code base,
but it is explicit, easy to find, and easy to control.

Many of the simpler changes needed to update Python 2 code to also support
Python 3 can be made automatically by the `python-modernize`_ script.  For
example::

    python-modernize -w path/to/package

.. _python-modernize: https://pypi.org/project/modernize/

Third-party dependencies
========================

Before converting a codebase to Python 3, we need to make sure the code we
depend on will also support Python 3.  We can make a rough check for
third-party library support using `Can I Use Python 3`_, either as a website or
library.  This will sometimes show inaccurate
results, as it depends upon self-reporting of library compatibility (via the
library's ``setup.py`` classifiers), but will help guide our investigations and
scope out the amount of work required.  Results can be tracked in
`requires.io`_ (for example, here are the
`results for edx-platform <https://requires.io/github/edx/edx-platform/requirements/?branch=master>`_).

.. _requires.io: https://requires.io/

If a required library does not support Python 3, we have a few options:

1. We can contribute a patch to support Python 3 to the library.
2. We can request Python 3 support, and wait for the maintainers to implement it.
3. We can find an alternative library that does support Python 3.

Which path is best may depend on the enthusiasm of the maintainers for
supporting Python 3, the amount of resources we want to commit to the project,
and the availability and quality of alternatives.

\_\_future__ imports
====================

All files should have the main ``__future__`` imports at the top to regularize
some behaviors that differ by default between Python 2 and 3.

* ``from __future__ import absolute_import`` prevents the use of implicit
  relative imports
* ``from __future__ import print_function`` makes print a function instead of a
  statement.
* ``from __future__ import division`` will make single-slash division
  (``a / b``) always perform floating point division, and double-slash division
  (``a // b``) perform integer division.

Text handling
=============

Text handling is the largest area of difficulty in porting Python.  Where
possible, we will use unambiguous text or byte objects.  In most cases, text
should be preferred.  Bytes should only be used when you can answer the
question: "Do I need this specific sequence of bytes."  The most
error-resistant way to achieve this is to use what is called a "unicode
sandwich."  This means that as soon as you receive data from a file or network
interface, it should be converted to text. Your code should then treat it as
text for as long as possible, only encoding it back to bytes when sending it to
an interface that requires bytes (such as a file, a network interface, or a
bytes-oriented library). The only operation that should (ideally) be performed
on bytes is decoding.

In those cases where ambiguity is required (such as working with libraries like
``csv`` which require byte strings in Python 2 and unicode strings in
Python 3), we should isolate the need for ambiguity as much as possible.  Type
checking libraries like PyContracts_ (already used in edx-platform) or typing_
(a backport of the type hinting system introduced in Python 3.5) can help us
ensure that callers are using the appropriate variety of string.

If you need to create bytes, and there is no compelling reason to use a
specific encoding, use utf-8.  Compelling reasons include requirements of a
particular data format or protocol, or requirements of legacy or third-party
libraries.

If you need to accept bytes, and we have the freedom to require a particular
encoding, require utf-8.  If we need to support multiple encodings, require
that inputs specify their encoding explicitly, or be treated as utf-8.  Refuse
the temptation to guess anything other than utf-8.  Misencoded inputs should
ideally be rejected as an error. If that is not an option, malformed characters
should be replaced with the unicode replacement character, `U+FFFD`_.  If you
need to accept bytes from an interface that doesn't specify its encoding, pass
it through a wrapper that does specify the encoding, and use that wrapper
instead.

.. _U+FFFD: http://unicode-table.com/en/FFFD/

There are two major ways of handling text and byte literals uniformly across
Python versions.  We do not explicitly require one way over the other, but
decisions should be made on a per-project basis, and adhered to by all
developers working on that project.

One potential 'gotcha' to look out for is in your ``setup.py`` files. Per the
documentation for distutils_, none of the string values for metadata fields may
be unicode. This has the potential to cause problems_ when using a python 3
ready distribution in a python 2 project.

.. _distutils: https://docs.python.org/2/distutils/setupscript.html#additional-meta-data
.. _problems: https://github.com/edx/XBlock/pull/365

Handling literals, Option 1: Python 3-Style
-------------------------------------------

In order to write code that looks as much like native Python 3 as possible,
you may want to use ``from __future__ import unicode_literals``, which makes bare
string literals like ``'this'`` create text objects (``unicode`` objects in Python
2, ``str`` objects in Python 3), while bytes (``str`` objects in Python 2, ``bytes``
objects in Python 3) are created with b-prefixed string literals, such as
``b'this'``.  Native ``str`` objects do not exist in this system, but have wildly
inconsistent behavior anyway.  If they are needed for libraries that require
different types for different version of Python, they be created with text
(unicode) objects and explicitly encoded to bytes for Python 2.

.. code:: python3

    from __future__ import unicode_literals
    import six

    x = 'foo'
    if six.PY2:
        x = x.encode('utf-8')

This code will look more like clean Python 3, but requires changing code one
full file at a time, at a minimum.  Even then, it creates non-local semantics
for text and byte literals, so it would be better to make the changes more
broadly (one full repo or at least djangoapp at a time).

Handling literals, Option 2: Explicit unicode literals
------------------------------------------------------

Because of the difficulty in mentally context switching between code that uses
unicode-by-default strings, and bytes-by-default strings in a single Python 2
codebase, you may want to avoid the use of ``from __future__ import
unicode_literals``, but instead recommend using explicit ``u'unicode'`` and
``b'byte'`` literals throughout. Bare native-string literals should be used
sparingly, and explicitly called out as intentional usages.  This "calling out"
can be enforced by installing the ``caniusepython3`` pylint extension, which will
flag a warning (``native-string``) on such uses.  A native string would then be
instantiated as:

.. code:: python3

    native = 'string'  # pylint: disable=native-string

This version creates noisier code than Option 1, above, but makes it easier to
incrementally migrate large files, without introducing breaking changes.

Builtins
========

The Python standard library has been shuffled around a bit in the move to
Python 3.  With ``six``, most changed
functionality is described in the list of renames under ``six.moves`` in the
documentation.  The recommended way to use this is just to put ``import six``
at the top of the file, and use the fully-qualified names, in order to be
clear about where we are using compatibility code.

Where possible, ``six`` is the recommended solution.

.. code:: python3

   import six  # Or use: from django.utils import six

   for bottlecount in six.moves.range(99, 0, -1):
       print("{} bottles of beer on the wall".format(bottlecount))

   assert isinstance(u'abc', six.text_type)
   assert isinstance(b'abc', six.binary_type)
   course_key_string = six.text_type(course_key)

Dictionaries and iterables views
================================

Instead of using ``d.iterkeys()``, use
``six.viewkeys(d)``.  If you need a list, use ``list(*.viewkeys(d))``.  Other
similar functions exist for ``itervalues()`` and ``iteritems()``.  These
changes cannot be made cleanly in the import headers, and will require more
work to change after the fact.  This can be avoided in some cases by iterating
directly over the dict object.  Instead of using:

.. code:: python3

    for key, value in six.viewitems(d):
        print(key, value)

You could do:

.. code:: python3

    for key in d:
        value = d[key]
        print(key, value)

Packaging
=========

All packages should maintain the proper trove classifiers for the versions of
Python they support.

In the following recommendations, the major version classifiers comprise:

.. code::

    Programming Language :: Python :: 2
    Programming Language :: Python :: 2 :: Only
    Programming Language :: Python :: 3
    Programming Language :: Python :: 3 :: Only

Minor version classifiers include, but are not limited to:

.. code::

    Programming Language :: Python :: 2.6
    Programming Language :: Python :: 2.7
    Programming Language :: Python :: 3.5
    Programming Language :: Python :: 3.6

Packages that do not yet support Python 3 should list both of the major version
Python 2 classifiers, plus any minor version classifiers that apply.

Packages that support both Python 2 and Python 3 should include major version
classifiers for both versions of Python, but must not include either of the
``:: Only`` classifiers.

Packages that have dropped Python 2 support should list both of the major
version Python 3 classifiers, plus any minor version classifiers that apply.

Ideally, all listed minor versions should be tested in a continuous integration
environment.  At a minimum, at least the lowest and highest minor versions of
each supported major version must be tested.

Other problems
==============

If you find other incompatibilities, a shim will likely be found as part of
``six``.  When writing code that explicitly switches based on version, do

.. code:: python3

    if six.PY2:  # or future.PY2
        do_python2_thing()
    else:
        do_python3_thing()

Do not explicitly call out ``six.PY3`` or ``future.PY3``.  This should be more
future-compatible with a potential future Python 4.


Changing Coding Practice
************************

Changing internal code practices to ease conversion will require a
three-pronged approach of documentation, socialization, and tooling.  To start,
we need to update the official edX code style guide to mandate compatible code
practices.  To socialize these practices among our engineers, we will announce
our efforts to migrate to Python 3 during an engineering all-hands meeting,
offer a workshop in writing compatible code, and promote awareness of
incompatibilities during code reviews.  Additionally, we will host regular
Python 3 office hours to help answer questions and troubleshoot problems that
arise during migration.

Appropriate tooling will help.  Tests should be configured to run under both
Python 2 and Python 3.  A lightweight metric to measure conversion before tests
can successfully run under Python 3 will also be useful. For this, we should
run pylint with the ``caniusepython3.pylint_checker`` extension.  Making these
checks mandatory in a similar way to our current quality will ensure that
compatibility is improving.


Migrating projects
******************

We should be able to migrate individual applications to Python 3 independently.
To begin with, we should pilot the process using a relatively small (but
complex enough to provide useful information) IDA.  As we go, we will document
the process, find pain points, figure out ways of dealing with them, and
continue to improve our process.

For a given project, steps are:

1. Turn on caniusepy3k linting, and reset the lint error cap.
2. Turn on tox testing in Python 3, but allow the tests to fail.
3. Reduce the number of lint errors to zero, lowering lint error cap as you go.
   Optionally, use ``futurize``, phase 1 to automate the first stage of the
   conversion process.
4. Reduce the number of failing tests to zero.  This may involve updating
   dependent libraries to Python 3 compatibile versions.  It will almost
   certainly involve normalizing text handling.
5. Make failing Python 3 tests fail the build.
6. Deploy the project in Python 3.
7. Stop testing under Python 2.


Order of migrations
*******************

* IDAs that we want to continue supporting in the future

  * Old IDAs (that we want to replace) should not be upgraded, but we will need
    to prioritize replacement to occur during the migration timeframe.

* Implement remote execution of xBlocks (to allow a window of bicompatibility
  for external xblocks)
* edx-platform

  * Deploy xblocks separately to test remote execution.
  * Add support for external graders using either Python 2 or Python 3.
  * Migrate to Python 3.
  * Upgrade external xblocks as needed, and support partners who wish to do the
    same.

Support libraries should be migrated as required by our migration schedule for
the services that require them.  If external libraries need minor updates to
support Python 3 that we can perform, we should opt to push those changes
upstream rather than forking projects when possible.

Code conversion should be automated as much as possible.  The modernize_ package
includes a ``python-modernize`` executable that will do much of the legwork.  As we gain
experience migrating code, we will develop a sense as to how aggressively we
can use ``python-modernize``, and what other work needs to be done.  There is
a page on the Open edX Confluence wiki capturing the current state of
`updating edx-platform to work under Python 3`_

.. _modernize: https://pypi.org/project/modernize/
.. _updating edx-platform to work under Python 3: https://openedx.atlassian.net/wiki/spaces/AC/pages/939065888/edx-platform+Python+3+Upgrade+Plan


Deprecating Python 2
********************

Once a project has been converted to Python 3 and deployed, and there is no
further need to support the Python 2 version, we will deprecate the Python 2
version of the project. The first step is to document that the Python 2 version
is no longer supported.  Then we can stop testing against Python 2. Finally, we
can begin cleaning out compatibility code from the code base.

Open source libraries we maintain (that are useful beyond their integration
with our own projects) should continue to support Python 2 until Python 2 is
EOLed in 2020.

Supporting external partners
****************************

We intend to be as transparent as possible about this process with Open edX
users, and partner institutions.  This document will be updated to reflect
support needs that we learn about in communication with external stakeholders,
including policies for advance notification and transition support.

.. _Future: https://python-future.org/
.. _Six: https://pythonhosted.org/six/
.. _Tox: https://tox.readthedocs.io/
.. _Can I Use Python 3: https://caniusepython3.com/
.. _PyContracts:
.. _typing:
