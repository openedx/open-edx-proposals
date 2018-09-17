===========================
OEP-0029: Real Time Theming
===========================

+-----------------+----------------------------------------------------------------+
| OEP             | :doc:`OEP-0029 </oeps/oep-0028-arch-real-time-theming>`        |
|                 |                                                                |
|                 |                                                                |
|                 |                                                                |
|                 |                                                                |
+-----------------+----------------------------------------------------------------+
| Title           | Real Time Theming                                              |
+-----------------+----------------------------------------------------------------+
| Last Modified   | 2018-09-06                                                     |
+-----------------+----------------------------------------------------------------+
| Authors         | Matjaz Gregoric <matjaz@opencraft.com>,                        |
|                 | Taranjeet Singh <taranjeet@opencraft.com>                      |
+-----------------+----------------------------------------------------------------+
| Arbiter         |                                                                |
+-----------------+----------------------------------------------------------------+
| Status          | Draft                                                          |
+-----------------+----------------------------------------------------------------+
| Type            | Architecture                                                   |
+-----------------+----------------------------------------------------------------+
| Created         | 2018-09-06                                                     |
+-----------------+----------------------------------------------------------------+
| `Review Period` | 2018-09-06 - 2018-09-23                                        |
+-----------------+----------------------------------------------------------------+

Abstract
-------

OEP-0023 [1] addresses the styling part of a theming system based on React. This proposal adds guidelines to allow updating theme dynamically and develop features that will allow to apply any theme from Open edX interface.

Context
-------

In Open edX style changes are currently applied by overriding sass variables and adding custom sass/css. This will remain true even after the switch to the new theming system based on edx-bootstrap. This further means that sass recompilation is required for typical style changes. In OEP-0023 [1], one of the consequences is listed as “The Open edX platform’s static asset size and build times will be greatly reduced.” This holds true as ``paver update_assets`` takes around 8 minutes on Hawthorn sandboxes.

There is no reasonable way to avoid sass recompilation, since sass variables are used in multiple sass files in various repositories. The variables are not merely used as CSS values, but are also used in sass logic and computations. Simply generating a CSS file that overrides styles from the platform is therefore not reasonable.

At times, multiple version of edx-platform are needed to run in parallel and so the theming system should make sure that the stylesheets don't conflict with each other. For example, when upgrading from one Open edX release to the next, we want to make sure that deploying appserver for the next release won’t suddenly make the old appservers use the new release’s stylesheets.

Decision
--------

A dynamic theming system will work in the following way.

* All stylesheets that are produced by sass compilation will be stored using the django File storage API. Files will be stored on local filesystem in devstack and on S3/Swift in production environment. This will allow the django app to use the most recently compiled stylesheets without having to run sass recompilation on all edxapp VMs.

* A stylesheet would be named as a compound key consisting of ``site_id``, ``sass_overrides_id``, ``stylesheet_name`` and ``input_sass_md5``. This will keep the styles consistent and allow using multiple versions.

* The ``sass_overrides_id`` field is a foreign key that points to customized sass variables. The sass overrides table will be similar to site configurations table in that any update will produce a new immutable record, with a new ID. Each record will have a status field that can be used for preview/publish control. We will reuse the existing LMS preview subdomain for previewing pending updates to the theme.

* The ``input_sass_md5`` values is the MD5 hash of the raw contents of the sass input files (without the overrides). The ``input_sass_md5`` will change from release to release and will be used in aggressive caching.

* Admin users will be able to override default values of edx-bootstrap sass variables from the django admin. The custom values get stored to the database.

* Admin will click on “Update Theme” button once sass variables tweaking is finished. This will launch sass recompilation step in the background via a celery task. Sass compilation will use sass variables from the database. While sass compilation is in progress, the UI will display a notice and prevent user from triggering another recompilation until the current one completes.

* When sass compilation completes, it will write the new stylesheets to storage, where django picks them up and starts using the new stylesheets.

* The value of ``input_sass_md5`` calculated will be used to decide which file CSS file to read from the storage via any repository code.

* When deploying a new instance, sass will still be complied using overridden values from the database. This will ensure that stylesheets are generated as part of the deployment process and not triggered via any admin action. The MD5 hashes will ensure that complied stylesheets don't interfere with each other.

Consequences
------------

It's true that the real time theming systems are valuable and ideal from an admin/maintainer experience standpoint, but the technical complexity involved in enabling it for new and legacy systems may be a burden.

References
----------

1. OEP-0023 Style Customization
      https://open-edx-proposals.readthedocs.io/en/latest/oep-0023-style-customization.html
