
=============================
OEP-0049: Django App Patterns
=============================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-0045 </oeps/oep-0049>`
   * - Title
     - Django App Patterns
   * - Last Modified
     - 2021-01-29
   * - Authors
     - Matt Tuchfarber <mtuchfarber@edx.org>
   * - Arbiter
     - Jinder Singh
   * - Status
     - Under Review
   * - Type
     - Architecture
   * - Created
     - 2021-01-29
   * - Review Period
     - 2021-02-22 - 2021-04-10

.. contents::
     :local:
     :depth: 2

Abstract
--------
Proposes a common set of code patterns for Open edX Django apps.

Motivation
-----------
As our number of Django apps continue to grow in our many services, we want to coalesce around a couple of standard design patterns to both make switching between codebases easier and to help untangle some of the links between codebases we have today. These decisions should be considered "best practices" or the default patterns, and should only be violated if the code base requires it.

Decision
--------
All of our Django apps should have a common structure. This structure consists of a combination of default Django-style code and Open edX-style code. This document will only attempt to detail the common Open edX patterns that we would like to see everywhere, ignoring Django-default items (e.g. ``admin.py``, ``urls.py``, etc) and situation-specific items (e.g. a separate ``constants.py`` file).

The common Django files and folders this **won't** set preferences for are:

.. list-table::
   :widths: 25 60 15

   * - admin.py
     - The additions to the Django admin site live here
     - `Docs <https://docs.djangoproject.com/en/3.1/ref/contrib/admin/>`_
   * - migrations/
     - The migrations for the models live here
     - `Docs <https://docs.djangoproject.com/en/3.1/topics/migrations/>`_
   * - management/
     - The management commands live here
     - `Docs <https://docs.djangoproject.com/en/3.1/howto/custom-management-command/>`_
   * - models.py
     - The Django ORM models live here
     - `Docs <https://docs.djangoproject.com/en/3.1/topics/db/models/>`_
   * - urls.py
     - The URL structure of your app is defined here
     - `Docs <https://docs.djangoproject.com/en/3.1/topics/http/urls/>`_

More detailed

Listed below are each of the files or folders your app should contain and what they should consist of.


README.rst
++++++++++
Each app should contain a README.rst to explain its use. See full details of what should go in the README.rst in OEP-0019_

.. _OEP-0019: https://open-edx-proposals.readthedocs.io/en/latest/oep-0019-bp-developer-documentation.html#readmes

.. ___init__.py:
__init__.py
+++++++++++
The ``__init__.py`` file should contain a line for the ``default_app_config`` for the app. This ``default_app_config`` should point to the ``AppConfig`` located in ``<app_name>/apps.py``. It may also contain small app details such as a version. However, unlike many packages, ``__init__.py`` should *not* be used to as the way to export the app's public methods. These should be exported using ``api.py`` (and thus imported as ``from path.to.app.api import public_function``). See api.py_ below.

Example
~~~~~~~

.. code-block:: python

  default_app_config = "service_name.apps.app_name.apps.CustomAppConfig"


.. _apps.py:
apps.py
+++++++
The ``apps.py`` file should contain a subclass of a Django ``AppConfig``. The AppConfig should set the app's name to its full path (e.g. ``name = "service_name.apps.app_name"``) and should (optionally) have an overriding ``ready()`` function which initializes the app. Any imports that need to happen during app initialization (such as signals_) need to happen inside the ``ready`` function or else there's risk of circular imports.

Example
~~~~~~~

.. code-block:: python

  class MyAppConfig(AppConfig):
      """
      Application Configuration for MyApp.
      """
      name = "service_name.apps.app_name"

      # (optional) Set up plugin. See https://github.com/edx/edx-django-utils/tree/master/edx_django_utils/plugins

      def ready(self):
          """
          Connect handlers to recalculate grades.
          """
          from .signals import handlers

.. _api.py:
api.py
++++++
This should be single point of entry for other Python code to talk to your app. This is *not* a Rest API, this is a Python API (see rest_api_). Some rules for ``api.py`` are as follows:

1. API methods defined in ``api.py`` should be well-named, self-consistent, and relevant to its own domain (without exposing technical and implementation details)
2. An app's Django models and other internal data structures should not be exposed via its Python APIs (unless performance requires it).


Not exposing an app's data structures can be tricky because it's very easy to expose them without meaning to. Therefore there are a couple common strategies we employ.

1. When importing internal app code to be used in the ``api.py`` file, prefix it with an underscore so it's clear it's for internal use only.

2. Create a ``data.py`` file to house simple data objects that can be passed from your app's function to the calling app. By creating these objects, we can avoid both passing Django model objects or querysets directly and having to serialize data. Other apps may import data classes from ``data.py`` in additional to functionality from ``api.py``. See data.py_ for more details.

Performance caveat
~~~~~~~~~~~~~~~~~~
While there are many situations that the above solution works well for, there are a number of situations where the need for performance outweighs the preference for strong code boundaries. In these situations, APIs may return querysets of models so the code consuming the API may efficiently filter and retrieve the data. We don't have solutions that keep strong boundaries and have good performance today, but are working towards them.

If you simply need to page your results and want to keep code boundaries intact, you can use Django's Paginator class to keep the retrievals performant without passing Querysets around.

Example
~~~~~~~

.. code-block:: python

  from django.conf.settings import UNSUPPORTED_PROGRAM_UUIDS
  from django.core.paginator import Paginator

  from .data import ProgramData
  from .models import Program as _Program

  def get_supported_programs_simple():
    """
    Gets all programs that aren't in UNSUPPORTED_PROGRAM_UUIDS settings
    """
    supported_programs = _Program.objects.exclude(
        uuid__in=UNSUPPORTED_PROGRAM_UUIDS
    )

    return [
        ProgramData(
            uuid=program.uuid,
            title=program.title,
            status=program.status
        )
        for program in supported_programs
    ]

  def get_supported_programs_paged(page_size=None, page=None):
      """
      Gets all programs that aren't in UNSUPPORTED_PROGRAM_UUIDS settings

      Returns a page of results if page_size is specified
      """
      q_supported_programs = _Program.objects.exclude(
          uuid__in=UNSUPPORTED_PROGRAM_UUIDS
      )

      if page_size:
          # passing a queryset to a paginator allows it to stay performant on large tables
          program_paginator = Paginator(q_supported_programs, page_size)
          # get_page returns the first page if page is None
          supported_programs = program_paginator.get_page(page)
      else:
          supported_programs = q_supported_programs

      return [
          ProgramData(
              uuid=program.uuid,
              title=program.title,
              status=program.status
          )
          for program in supported_programs
      ]

.. _data.py:
data.py
+++++++
This file should include the public data structures for the app that can be passed between apps without exposing internal features. These should be used instead of sending Django model objects or querysets to apps that call the functions in ``api.py``. This file should not import anything other than stdlib modules, so that it may be imported by any other app without issue. These data objects should be simple objects with all business logic handled by ``api.py``. They may however perform simple validation, as long as it is self-contained (doesn't reach out to database, network, or any code outside of the class)

Example
~~~~~~~

.. code-block:: python


  from enum import Enum

  import attr

  def ProgramStatus(Enum):
      ACTIVE = "active"
      RETIRED = "retired"

  @attr.attrs(frozen=True)
  class ProgramData:
      uuid: str = attr.attrib(
          validator=[attr.validators.instance_of(str)]
      )
      title: str = attr.attrib(
          validator=[attr.validators.instance_of(str)]
      )
      status: str = attr.attrib(
          validator=[
              attr.validators.instance_of(str),
              attr.validators.in_(ProgramStatus)
          ]
      )

.. _rest_api:
rest_api/
+++++++++
If an app will have its own REST API, it should live in a folder called ``rest_api`` to distinguish it from the ``api.py`` file used for intra-app communication.

APIs should be versioned and the serializers and permissions associated with that version should be kept inside that version's folder. This prevents breakages when an API needs to be updated.

An example of a common folder structure for a versioned REST API::

  app_name
  ├── rest_api
  │   ├── v1
  │   │   ├── permissions.py
  │   │   ├── serializers.py
  │   │   ├── urls.py
  │   │   └── views.py
  │   └── urls.py
  ├── urls.py
  └── views.py  # existing legacy non-REST APIs

Because API conventions (including URL structure, namespacing, and versioning) are separate concerns than the app structure, please reference https://openedx.atlassian.net/wiki/spaces/AC/pages/18350757/edX+REST+API+Conventions for any questions.


.. _signals:
signals/
+++++++++
If an app is consuming Django Signals from other apps or creating its own Signals, it should include a ``signals`` directory which will include both its signal handlers and Signals it owns. If possible, the signal handlers should only be thin layer between the signal and more generalized functions in the app. This way we can keep business logic out of the "plumbing". The signals directory should look like::

  app_name
  ├── signals
  │   │   ├── signals.py  # for defining new signals
  │   │   ├── handlers.py  # for listening to existing signals

.. _tasks:
tasks/ or tasks.py
++++++++++++++++++
If an app contains long running tasks (i.e. tasks that run outside of a request, often a celery task), they should live in in either either a ``tasks.py`` file or a ``tasks`` folder.

Consequences
------------
At this time, there is no plan to enforce any of these guidelines. The vast majority of current Open edX code doesn't yet meet these guidelines, and there will always be exceptions to the rule. The hope is that as developers write new code or refactor existing code, they follow these patterns as best they can. We also hope that code reviewers will ensure these guidelines are followed in the code they approve.
