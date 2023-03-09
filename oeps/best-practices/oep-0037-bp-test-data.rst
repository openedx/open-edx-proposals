OEP-37: Dev Data
##################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-37 <oep-0037-bp-test-data>`
   * - Title
     - Devstack Dev Data
   * - Last Modified
     - 2019-11-25
   * - Authors
     - Jeremy Bowman <jbowman@edx.org>
   * - Arbiter
     - Bill DeRusha <bill@edx.org>, Feanil Patel <feanil@tcril.org>
   * - Status
     - Deferred
   * - Type
     - Best Practice
   * - Created
     - 2019-06-07
   * - Review Period
     - 2019-06-19 to 2019-11-25


Abstract
********

Proposes best practices in creating and maintaining sample dev data for use in Open edX test cases and development environments.

Motivation
**********

When testing or developing an Open edX service, it is usually necessary to have some dev data already loaded and configured: users, courses, grades, etc.  This is true for unit tests, browser automation tests, load tests, development work in devstack, and more.  There are many different ways of creating and loading such data, and in the absence of concrete guidance on best practices, many of them have been (inconsistently) used in Open edX.  This makes it difficult to decide how to prepare such dev data, to learn how to load it when needed, and also to keep it working correctly as the code changes over time.

This OEP attempts to provide the guidance needed to manage this data logically and consistently in order to streamline development, refactoring, and automated testing.

Specification
*************

There are a few different aspects of dev data management, each of which has different tools that can make it more painless:

Reusing Data
============

Once a development environment has been populated with a dev data set comprehensive enough to exercise certain functionality in Open edX, it can be useful to "save it" for later use.  The first step is to make sure that any `PII`_ has been stripped from the data.  Even development data may include a developer's email address and other personal information.  Tools such as `dj_anonymizer`_ can be useful in making a first pass at stripping such PII, but it should be followed by manual verification against the `OEP-30`_ annotations.

Once PII has been removed, an attempt can be made to export just the needed data.  This may be a combination of course export to OLX and generation of fixtures from selected relational data using a utility such as `django-fixture-magic`_.  These fixtures should not be saved verbatim, but rather used as a guide for producing Python code that generates roughly equivalent data using ``factory_boy`` as described below.

Note that if the following recommendations on creating data are followed, test data created for a unit test can be fairly easily re-purposed for use as development environment data sets.  This can be useful because the starting point is already free of PII and has been verified by tests to work as intended.

.. _PII: https://open-edx-proposals.readthedocs.io/en/latest/oep-0030-arch-pii-markup-and-auditing.html
.. _dj_anonymizer: https://github.com/preply/dj_anonymizer
.. _OEP-30: https://open-edx-proposals.readthedocs.io/en/latest/oep-0030-arch-pii-markup-and-auditing.html
.. _django-fixture-magic: https://github.com/davedash/django-fixture-magic

Creating Data
=============

Whether defining it from scratch or basing it on a dump of data from an existing environment, at some point code needs to be written which will populate the data stores of each relevant service in order to perform the desired test or development task.  For relational data in simple unit tests, this is often done in Python via the `factory_boy`_ package (and custom model factories using it) directly in ``setUpTestData`` or ``setUp``.  For development data (or more complex test setup which could be useful to reproduce in a development environment), the setup code should instead reside in its own function or static method.  For example:

.. code-block:: python

    def account_retirement_example(data):
        data.test_user = UserFactory.create()
        data.test_superuser = SuperuserFactory.create()
        data.original_email = data.test_user.email

        # Misc. setup
        PendingEmailChangeFactory.create(user=data.test_user)
        UserOrgTagFactory.create(user=data.test_user, key='foo', value='bar')
        UserOrgTagFactory.create(user=data.test_user, key='cat', value='dog')

        CourseEnrollmentAllowedFactory.create(email=data.original_email)

(JavaScript tests seeking to stub out data returned from a server can use similar functions utilizing a package like `rosie`_ or `factory-girl`_.)

Setting attributes of the provided argument like this allows the test object or class to be provided when used in a unit test (so assertions can be written naturally), and something like a `SimpleNamespace`_ object can be provided when run from a developer console so the generated data can be quickly located and inspected.  When needed, course data should be loaded from an `OLX`_ archive via ``call_command()`` using the existing management commands for course import.

Only the attributes needed to perform the intended task should be specified; the factory class should take care of setting reasonable defaults for the rest.  The `Faker`_ package can be used by factories to generate reasonable-looking values for many common types of data.  By auto-populating many of the fields this way, when a model changes often only the factory will need to be updated rather than every data set using that model.

The function can take one or more positional arguments if useful for using it in different contexts.  It could for example create records linked to a provided existing user, or populate them based on information loaded from a YAML file found at the provided path.  Keyword-only arguments should usually be avoided, as they are more difficult to use from the kind of general-purpose management commands and ``make`` targets described below.

.. _factory_boy: https://factoryboy.readthedocs.io/en/latest/index.html
.. _rosie: https://www.npmjs.com/package/rosie
.. _factory-girl: https://www.npmjs.com/package/factory-girl
.. _SimpleNamespace: https://docs.python.org/3/library/types.html#types.SimpleNamespace
.. _OLX: https://edx.readthedocs.io/projects/edx-open-learning-xml/en/latest/index.html
.. _Faker: https://faker.readthedocs.io/en/stable/

Loading Data
============


The dev data to be loaded should be specified in a yaml file and each IDA should provide a ``load_dev_data`` management command which takes a path to yaml files.  For example, the command to load the example data above into the LMS might look something like this:

.. code-block:: bash

    ./manage.py lms load_dev_data /tmp/example.yaml


If there is corresponding dev data that needs to be loaded into another IDA(i.e, ecommerce) when testing this functionality in devstack, there would be an equivalent command in the ecommerce repository:

.. code-block:: bash

    ./manage.py load_dev_data /tmp/example2.yaml

To keep data loading modular, the dev data specification for each IDA should live in its own yaml file.

Each ``load_dev_data`` management command should take the specification from the yaml file and call on the data generation fuctions that correspond to keys in yaml file.

Each data loading function should be executed during the respective IDA's test suite, in order to ensure that it stays functional across schema and code changes.  This also makes it clear what change triggered failure to load the data, making it much faster to make the appropriate fixes.

Because the dev data doesn't contain primary keys, loading the same data a second time into an environment will usually result in a second copy of the dev data being created in it.  If there is desire for a set of dev data that can be updated or reset in an environment in which it already exists, its data loading functions should be deliberately written to be capable of either deleting or updating any data they previously loaded (via known field value lookups, etc.)

Data Files
----------

Dev data for an individual IDA will be specified in a YAML file.  The path or URL of this file is passed to the ``load_dev_data`` management command, which uses the information in it to call the appropriate data generation function to create database records for a particular service as shown above.

These data files should be as minimal as possible, containing just enough information for a data loading function familiar with this format to generate appropriate records using factory classes to fill in reasonable defaults for anything not explicitly specified. This is to increase robustness to code changes and to keep the maintanance cost of these files as low as possible.

Such a file might look like this:

.. code-block:: yaml

   users:
    - username: verified
      email: verified@example.com
    - username: robot1
      email: robot1@example.com
  enrollments:
    - username: verified
      course_id: 'course-v1:edX+DemoX+Demo_Course'
      mode: verified
    - username: robot1
      course_id: 'course-v1:edX+DemoX+Demo_Course'
      mode: verified
    ...

Rationale
*********

Django provides utilities for managing data fixtures as JSON, XML, or YAML documents, but we and most other large projects have found such fixtures difficult to maintain over time; they need to be updated with most schema changes, require the specification of primary keys which are likely to conflict with existing data, and are structured in a way that makes it difficult to group together related dev data of different models.  We have chosen in our unit tests to use ``factory_boy`` instead, and it has served us well enough that we should try using it for development data sets as well.  Having a good set of data factories should make it much easier to maintain our dev data over time, and much of this work has already been done for the unit tests of each service.

Even utilizing ``factory_boy``, manually creating a set of data comprehensive enough to use a wide range of Open edX features in devstack is very challenging.  Using real data which was created during normal usage of Open edX can dramatically reduce the time needed to create a data set, but runs a high risk of leaking `PII`_ and once captured as detailed fixtures exhibits all the same problems noted above for standard Django fixtures.  So we want the ability to anonymize and dump subsets of data from an existing environment, but should convert it to data-loading code rather than rigid data fixtures.  We don't yet have good tools to do this conversion automatically, so at first this will be a mostly manual process (but should save us much time later in maintaining the data set through schema migrations and code changes).

Backward Compatibility
**********************

``factory_boy`` is already used extensively in most of the Open edX service test suites, and model factories have already been created for most of the key models.  The existing development data sets are scarce and generally under-utilized; they use a mix of devstack ``make`` targets, IDA management commands, custom JSON data files, etc.  The proposal outlined above is generally compatible with this prior art while offering more structure and guidance on developing and maintaining these data sets in the future.

Reference Implementation
************************

For test suite data, see the usage of ``factory_boy`` and OLX in edx-platform and other repositories.  For development data, the ``create-test-course`` and ``build-courses`` devstack ``make`` targets (as well as ``programs/provision.sh``) should be reviewed and refactored to comply with the guidelines outlined here.

Rejected Alternatives
*********************

* Django fixtures are easy to generate from existing data, but hard to create from scratch and fragile to schema and code changes.  The hard-coding of primary key values makes them particularly difficult to load in an environment that already has some data in it.  Given that we gave up on them even for simple unit test fixtures, it doesn't make sense to use them for more complex development data sets either.

* SQL dumps work for bootstrapping a test or development environment from scratch, but are effectively useless for adding data to an existing database.  This significantly reduces the utility of tools such as `XDump`_ and `django-sanitized-dump`_.

* There are alternatives to ``factory_boy`` for simplified fixture management (such as `model-mommy`_), but they seem to have few advantages over ``factory_boy`` which is already used extensively in Open edX.

.. _XDump: https://github.com/Stranger6667/xdump
.. _django-sanitized-dump: https://github.com/andersinno/django-sanitized-dump
.. _model-mommy: https://model-mommy.readthedocs.io/en/latest/


Related Decisions
*****************

The following related decisions modify or enhance this OEP, but have not yet been fully incorporated as updates to this OEP:

.. toctree::
   :caption: OEP-37 Decisions
   :maxdepth: 1
   :glob:

   oep-0037/decisions/*

Change History
**************

A list of dated sections that describes a brief summary of each revision of the OEP.
