OEP-9: User Authorization (Permissions)
#######################################

+---------------+-----------------------------------------------------------------------+
| OEP           | :doc:`OEP-9 <oep-0009-bp-permissions>`                                |
+---------------+-----------------------------------------------------------------------+
| Title         | User Authorization (Permissions)                                      |
+---------------+-----------------------------------------------------------------------+
| Last Modified | 2023-10-02                                                            |
+---------------+-----------------------------------------------------------------------+
| Author        | Jeremy Bowman <jbowman@edx.org>                                       |
+---------------+-----------------------------------------------------------------------+
| Arbiter       | Eddie Fagin <eddie@edx.org>                                           |
+---------------+-----------------------------------------------------------------------+
| Status        | Replaced by :doc:`OEP-66 </best-practices/oep-0066-bp-authorization>` |
+---------------+-----------------------------------------------------------------------+
| Type          | Best Practice                                                         |
+---------------+-----------------------------------------------------------------------+
| Created       | 2016-09-09                                                            |
+---------------+-----------------------------------------------------------------------+

.. warning::

   This OEP has been replaced by :doc:`OEP-66 </best-practices/oep-0066-bp-authorization>`

Abstract
********

Proposes best practices for implementing authorization in edX services such
that there's a clear separation between checking whether or not a user has
permission to perform a given action and the details of how that determination
is made.

Motivation
**********

To date, the implementation and verification of permissions have been somewhat
conflated in the edX codebase.  When a user attempts an action which is not
permitted for all users, the code typically directly checks properties of the
user: are they a superuser, do they belong to a particular group or have a
particular role, etc.  This has a few drawbacks:

* Implementation of new features can get held up waiting for business
  decisions on exactly which types of users should be allowed to perform them.
* This is often a violation of DRY ("Don't Repeat Yourself") which results in
  the same basic permission check being copied in multiple code locations,
  making it very difficult to consistently change its implementation (as may
  happen when a new type of user is introduced, or the need for a special
  exception becomes clear).
* Fine-grained permission checks have sometimes been avoided even when
  appropriate due to the difficulty of copying the permission code around,
  finding a common place to store it, or updating all the code that used a
  coarser initial implementation.  This has resulted in some users being
  technically capable of performing actions which logically should not be
  permitted.
* When a decision is made to change who is granted a particular permission,
  it can be difficult to avoid accidentally changing other permissions with
  a similar implementation.

Specification
*************

Most authorization checks in Python code should use the standard
`Django authorization API`_, including the optional support for object-level
permissions which isn't implemented in the default backends.  Some examples
can help give context for the details:

.. _Django authorization API: https://docs.djangoproject.com/en/1.10/topics/auth/default/#permissions-and-authorization

.. code-block:: python

   if user.has_perm('my_app.change_modelname', model_instance):
       # Code which depends on the user being allowed to edit that specific model instance

   if user.has_perm('other_app.add_othermodel'):
       # Code which depends on the user being allowed to create new instances of OtherModel

.. code-block:: python

   from django.contrib.auth.decorators import permission_required

   @permission_required('polls.vote')
   def my_view(request):
       # ...

Permission Names
================

Note that a permission name should respect the following rules:

* It should contain only lower-case ASCII letters, periods, and underscores.
* It should start with the name of a Django application followed by a period.
* It should follow an "action_modelname" pattern for the rest of the name
  if appropriate (especially because several of these are used by the Django
  admin interface if defined), or a short description of an action otherwise.

Rule-Based Authorization
========================

While the Django authorization API is quite flexible, many Django developers
have not really utilized it because the default authentication backend that
comes with Django lacks support for object-level permissions and requires the
addition of per-user database records for even the most trivial permission
checks.  Fortunately, Django supports custom authentication backends, and
checks each one that's in use when making authorization checks.  The backend
which we currently recommend for use in defining new permission checks is
`rules`_.  It makes no changes to the authentication of users trying to log
into the system, but allows the creation of new permissions by mapping
their name to a function which implements the check.  Django apps which are
implemented in the repository for a service should generally define their
custom permissions in a ``rules.py`` module where they will be automatically
loaded, as described in the documentation.  For example:

.. _rules: https://github.com/dfunckt/django-rules

.. code-block:: python

   import rules
   from rules.predicates import is_superuser

   @rules.predicate
   def is_report_owner(user, report):
       return report.owner == user

   rules.add_perm('my_app.view_report', is_report_owner | is_superuser)

This allows permissions to be named and implemented in one place, without
requiring any additional database configuration.  Note that reusable Django
applications should not automatically register implementations of their
permissions, as the actual services using them may need to implement their
own rules for them.  ``rules`` also provides an improved
``permission_required`` view decorator which support testing object-level
permissions; see the documentation for details.

Note that although the optional second argument to ``User.has_perm()`` is
often a model instance, it can technically be any Python object which contains
information relevant to the permission being tested.  This allows for even
greater flexibility in the kinds of authorization rules that can be
implemented.

QuerySets
=========

One drawback to rule-based authorization vs. explicit configuration of
permissions in the database is that it complicates the filtering of querysets
to return only permission-appropriate results.  Checking the rule function for
each result from the query both requires fetching more results than are needed
and is likely to throw off pagination numbers (with some pages even having no
results that pass the permission check).  So instead, an alternate
implementation of each rule that must be used as a queryset filter is needed.
There doesn't yet seem to be a good implementation of this that doesn't
require a lot of custom model manager methods, but such a package might work
something like this:

.. code-block:: python

    from django.db.models import Q
    from qpermissions import filter, perms
    from rules.predicates import is_superuser

    @filter
    def is_book_author(user):
        return Q(author=user)

    is_book_author_or_superuser = is_book_author | is_superuser

    perms['books.view_book'] = is_book_author_or_superuser

    Book.objects.filter(perms['books.view_book'](user))

This would allow filter implementations for specific permissions to be kept
separate from model implementations, enable reuse of common elements in
multiple permissions, and perhaps even permit reuse in filter definitions of
``rules`` predicates that only check ``User`` attributes.  An
`early version of this filtering API`_ was proposed as an addition to the
``rules`` library, but it was concluded that it would work better as a
separate package.

.. _early version of this filtering API: https://github.com/dfunckt/django-rules/issues/40

Django REST Framework
=====================

When using Django REST Framework to build a REST API, note that it has object
permissions and query filtering mechanisms which are designed to be compatible
with Django's authorization API.  This means they also work well with the
``rules`` authentication backend described above.  You can
`set the permissions policy`_ to a class such as `DjangoObjectPermissions`_
and DRF will automatically check the appropriate object permission whenever
performing an action on a single object.  That particular class always denies
permission to anonymous users and assumes that there are no ``view_*``
permissions relevant to viewing or listing objects; those points can be
changed if desired by creating a subclass, for example:

.. _DjangoObjectPermissions: https://www.django-rest-framework.org/api-guide/permissions/#djangoobjectpermissions
.. _set the permissions policy: https://www.django-rest-framework.org/api-guide/permissions/#setting-the-permission-policy

.. code-block:: python

   class DjangoObjectPermissionsIncludingView(permissions.DjangoObjectPermissions):
       authenticated_users_only = False
       perms_map = {
           'GET': ['%(app_label)s.view_%(model_name)s'],
           'OPTIONS': ['%(app_label)s.view_%(model_name)s'],
           'HEAD': ['%(app_label)s.view_%(model_name)s'],
           'POST': ['%(app_label)s.add_%(model_name)s'],
           'PUT': ['%(app_label)s.change_%(model_name)s'],
           'PATCH': ['%(app_label)s.change_%(model_name)s'],
           'DELETE': ['%(app_label)s.delete_%(model_name)s'],
       }

If additional information about the session is needed beyond the user's
identity in order to make a permission decision (for example, if an action
should only be allowed if the client has been granted a particular OAuth
scope, as outlined in `OEP-4`_), then a custom `BasePermission`_ subclass can
be implemented which both consults the Django authorization API and makes the
necessary checks against the session or other properties of the request
object.

.. _OEP-4: https://open-edx-proposals.readthedocs.io/en/latest/oeps/oep-0004.html

In order to filter the querysets used to generate list responses to only
include objects appropriate for the users permissions, an appropriate filter
class should also be set.  A generic implementation using the library
proposed above for mapping permissions to Q objects might look as follows:

.. code-block:: python

    from qpermissions import perms

    class DjangoPermissionRulesFilter(BaseFilterBackend):

        perm_format = '%(app_label)s.view_%(model_name)s'

        def filter_queryset(self, request, queryset, view):
            user = request.user
            model_cls = queryset.model
            kwargs = {
                'app_label': model_cls._meta.app_label,
                'model_name': get_model_name(model_cls)
            }
            permission = self.perm_format % kwargs
            if permission not in perms:
                return queryset
            return queryset.filter(perms[permission](user))

Such a class would be used in a view's `filter_backends`_ attribute or
could be used by default for all view classes which don't override it.

.. _BasePermission: https://www.django-rest-framework.org/api-guide/permissions/#custom-permissions
.. _filter_backends: https://www.django-rest-framework.org/api-guide/filtering/#setting-filter-backends

Rationale
*********

Discussions about authorization in Open edX have made slow progress
to date because they often got bogged down in the details of which
particular rules to use for making authorization checks (or at least what
kinds of rules to use, e.g. role membership) and how to pass the information
needed to make authorization decisions across service boundaries.  In the
meantime, working code has needed to make authorization decisions and in the
absence of concrete guidance has usually resorted to explicit User attribute
checks, with the corresponding problems outlined in the Motivation section
above.

Meanwhile, the implementation of `OEP-3`_ has required the ability to apply
authorization checks in a reusable application which should have no knowledge
of the exact business logic used to implement them.  To facilitate this and
break down the overall authorization topic into more manageable chunks, this
OEP was initiated to handle just best practices of how to be able to perform
authorization checks in a consistent manner that makes no unreasonable
assumptions about their implementation.  Here are some of the goals which have
shaped the recommendations:

* Maintain compatibility with Django admin, Django REST Framework, and other
  3rd-party Django packages (many of them use Django's authorization API, and
  a few even use object-level permissions)
* Reuse existing libraries when feasible
* Keep a clear separation between the implementation and usage of permissions
* Don't require database migrations or data loads each time a new permission
  is added or the implementation of one is changed

.. _OEP-3: https://open-edx-proposals.readthedocs.io/en/latest/oeps/oep-0003.html


Backward Compatibility
**********************

The ``rules`` package can be added to existing packages with minimal impact,
as it doesn't inherently change the outcome of any authorization checks.  In
the handful of places where Open edX code actually uses the Django
authorization API already, the permission implementation can be switched to
use ``rules`` when convenient (at which point whichever implementation is
already in use should be deactivated so they don't come to disagree over
time.)  New code can be written to follow the guidelines in this OEP, and
existing code can be gradually updated as the need arises.  However, effort
should be made to update all relevant code related to a new explicit
permission to avoid tempting developers into thinking they've fully updated
the implementation of a permission just by updating its rule while older code
still uses a copy of the original implementation.


Change History
**************

2023-10-02
===========

* Status updated to "Replaced by :doc:`OEP-66 </best-practices/oep-0066-bp-authorization>`"
* `PR #520 <https://github.com/openedx/open-edx-proposals/pull/520>`_
