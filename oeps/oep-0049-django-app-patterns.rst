
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
     - 2021-02-22 - 2021-03-10

Abstract
--------
Proposes a common set of code patterns for Open edX Django apps.

Motiviation
-----------
As our number of Django apps continue to grow in our many services, we want to coalesce around a couple of standard design patterns to both make switching between codebases easier and to help untangle some of the links between codebases we have today. These decisions should be considered "best practices" or the default patterns, and should only be violated if the code base requires it.

Decision
--------
All of our Django apps should have a common structure. This structure consists of a combination of default Django-style code and Open edX-style code. This document will only attempt to detail the common Open edX patterns that we would like to see everywhere, ignoring Django-default items (e.g. ``admin.py``, ``urls.py``, etc) and situation-specific items (e.g. a separate ``constants.py`` file).

Listed below are each of the files or folders your app should contain and what they should consist of.


README.rst
++++++++++
Each app should contain a README.rst to explain it's use. See full details of what should go in the README.rst in OEP-0019_

.. _OEP-0019: https://open-edx-proposals.readthedocs.io/en/latest/oep-0019-bp-developer-documentation.html#readmes

.. ___init__.py:
__init__.py
+++++++++++
The ``__init__.py`` file should either be blank if the app is a plugin, or contain a single line defining the ``default_app_config`` for the app. This ``default_app_config`` should point to the ``AppConfig`` located in ``<app_name>/apps.py``.

Unlike many packages, ``__init__.py`` should *not* be used to as the way to export the app's public methods. These should be exported using ``api.py`` (and thus imported as ``from path.to.app.api import public_function``). See api.py_ below.

For example:

.. code-block:: python

  default_app_config = "service_name.apps.app_name.apps.CustomAppConfig"


.. _apps.py:
apps.py
+++++++
The ``apps.py`` file should contain a subclass of a Django ``AppConfig``. The AppConfig should set the app's name to it's full path (e.g. ``name = "service_name.apps.app_name"``) and should (optionally) have an overriding ``ready()`` function which initializes the app. This initialization also often includes setting up Django signals.

For example:

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
2. An app's Django models and other internal data structures should not be exposed via its Python APIs.
3. Ideally, tests should use only Python APIs declared in other apps' ``api.py`` files. However, if an app's API is needed *only* for testing, then test-relevant Python APIs should be defined/exported in an intentional Python module called ``api_for_tests.py``.


Not exposing an app's data structures can be tricky because it's very easy to expose them without meaning to. Therefore there are a couple common strategies we employ.

1. When importing internal app code to be used in the ``api.py`` file, prefix it with an underscore so it's clear it's for internal use only.

2. Create a ``data.py`` file to house simple data objects that can be passed from your app's function to the calling app. By creating these objects, we can avoid both passing Django model objects or querysets directly and having to serialize data. Other apps may import data classes from ``data.py`` in additional to functionality from ``api.py``. See data.py_ for more details.

Using Django's Paginator class can help keep cross-app retrievals performant without passing Querysets around.

For example:

.. code-block:: python

  from django.conf.settings import UNSUPPORTED_PROGRAM_UUIDS

  from .data import ProgramData
  from .models_api import get_programs as _get_programs

  def get_supported_programs(page_size=None, page=None):
      """
      Gets all programs that aren't in UNSUPPORTED_PROGRAM_UUIDS settings

      Returns a page of results if page_size is specified
      """
      # _get_programs() returns a queryset
      q_supported_programs = _get_programs().exclude(
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

For example:

.. code-block:: python


  from dataclasses import dataclass
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

  # Alternatively if you don't need field validation, conversion, or other attrs benefits:

  @dataclass(frozen=True)
  class ProgramData:
      uuid: str
      title: str
      status: str

.. _rest_api:
rest_api/
+++++++++
If an app will have it's own REST API, it should live in a folder called ``rest_api`` to distinguish it from the ``api.py`` file used for intra-app communication.

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


.. _signals:
signals/
+++++++++
If an app is consuming Django Signals from other apps in the service, it should include a ``signals.py`` file which includes all of it's signal handlers.

Consequences
------------
At this time, there is no plan to enforce any of these guidelines. The vast majority of current Open edX code doesn't yet meet these guidelines, and there will always be exceptions to the rule. The hope is that as developers write new code or refactor existing code, they follow these patterns as best they can. We also hope that code reviewers will ensure these guidelines are followed in the code they approve.
