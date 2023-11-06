Tutor as a replacement for ``edx/configuration``
################################################


Status
******

Provisional


Context
*******

With the acceptance of OEP-0045, it becomes clear that `edx/configuration <https://github.com/openedx/configuration/>`__
will be deprecated and that a replacement is required.  However, OEP-0045 does not go into the details of what such an
alternative would look like.

This ADR identifies what criteria any replacement should meet, evaluates the `Tutor
<https://github.com/overhangio/tutor>`__ project against it, outlining work that needs to be done, and proposes the
latter as the choice of the community.


Criteria
********

Any replacement for ``edx/configuration`` must provide the following:

1.  A full, scalable deployment strategy for edx-platform and its main IDAs, supporting both small and large production
    deployments
2.  A mechanism that is suitable for automated deployment and testing
3.  Support for running the latest so-called "open release" of Open edX, and a method to upgrade from the previous one
4.  Support for running the master branch not only of edx-platform, but its corresponding IDAs
5.  Support for customizing of the installation, from themes to edx-platform forks, including installation of custom
    requirements
6.  A documented way to migrate existing deployments from the native installation
7.  Provides an Open edX development environment with features on-par with the official devstack's

And importantly:

8.  The project must be healthy, with thoughtful technical leadership, a well-organized, maintainable codebase, and an
    active community


Evaluation
**********

It is under the light of the previously described `criteria <#criteria>`__ that the Tutor project is evaluated.


Criterion 1. How well can Tutor deploy Open edX for production use?
===================================================================


Single server
-------------


Tutor's stated purpose is "To make it possible to deploy, administer and upgrade Open edX anywhere, easily." (`source
<https://docs.tutor.overhang.io/faq.html#what-is-the-purpose-of-tutor>`__).  And for single server installs, it clearly
lives up to it: in terms of ease-of-deployment, it is a step up from ``edx/configuration``. Getting a server up is
simpler than even using the official Docker devstack. A local test Koa deployment had one up and running in `under 15
minutes <#why-are-tutor-deployments-so-much-quicker>`__, where on the same machine a devstack deployment takes over 50,
and a full ``edx/configuration`` run takes close to 2 hours. And with remarkably few steps:

::

   git clone https://github.com/overhangio/tutor
   cd tutor
   pip install -e .
   tutor local quickstart

By default, this provides, each in a separate Docker container:

-  The LMS
-  Studio
-  LMS workers
-  CMS workers
-  The forum
-  MySQL 5.7
-  MongoDB 3.6
-  Redis 6
-  Elasticsearch 1.5
-  nginx 1.13
-  Caddy 2.2
-  An SMTP server

.. note::
   The above setup is new in Koa, specifically in that:

   - There is no longer a ``memcached`` instance.  Caching is now handles by Redis.
   - ``uwsgi`` instead of ``gunicorn`` as Python web servers. It serves media and static assets more efficiently.
   - The addition of Caddy for runtime letsencrypt SSL certificate generation.  (The author has not evaluated it against
     the Kubernetes-native ``cert-manager``, but this should be done as part of the work in ramping up Kubernetes
     support.)

And via `officially supported plugins <https://docs.tutor.overhang.io/plugins.html#existing-plugins>`__, one can easily
add these other official Open edX components:

-  Course Discovery
-  Ecommerce
-  Notes
-  Xqueue

There is a plugin for deploying the `Gradebook MFE <https://github.com/overhangio/tutor-gradebook/>`__, but so far none
of the others.  Given that not even ``edx/configuration`` currently supports this (though there is `a PR
<https://github.com/openedx/configuration/pull/6128>`__ for it, along with `instructions
<https://discuss.openedx.org/t/deploying-mfes-in-the-community/2868/22>`__), this should simply be added to the list of
things to add to Tutor.

.. note::
   The problem with MFEs is not deployment: it is theming.  Adding more deployment plugins to Tutor, or maybe creating a
   general one for MFE deployment, would not be as difficult as first preparing all existing and future MFEs for
   proper themability.


External databases
------------------

An important characteristic of scaling up an Open edX deployment is having the ability to point it to external
databases, such as Amazon RDS or a manually maintained Galera cluster. Tutor allows for just this. All of the following
can be disabled locally and easily `configured to use an externally hosted service
<https://docs.tutor.overhang.io/configuration.html#mysql>`__:

-  MySQL
-  Elasticsearch
-  MongoDB
-  Redis
-  SMTP


Multi-server
------------

Single-server deployments are less than ideal in terms of high availability and scalability, so for larger deployments
something different is needed. And given that Tutor deployments use Docker images, Kubernetes would be the first choice.
(Not least of which because Kubernetes is also what edX `has started to use
<https://discuss.openedx.org/t/lets-talk-about-the-native-installation/3269/14>`__.)

Tutor does come with basic support for Kubernetes deployment.  ``tutor k8s quickstart`` on a local `MicroK8s
<https://microk8s.io/>`__ 1.8 installation did not work out of the box, though. There is a race condition during the
startup of the MySQL container that requires relaunching the command, and one has to modify the MinIO hostname manually
to avoid a failure during the initial LMS job. There were further MinIO name resolutions issues that precluded a fully
working environment: there was no time to ascertain whether the cause is ultimately with Tutor or with the tested
microk8s setup.

It is mentioned at the top of the `Tutor Kubernetes documentation <https://docs.tutor.overhang.io/k8s.html>`__ that "the
local Open edX install was designed such that users with no prior experience with system administration could still
launch an Open edX platform. It is not the case for the installation method outlined here." While the Tutor project has
clearly expended effort in getting it to work with Kubernetes, if it is to fully replace ``edx/configuration`` as the
go-to community installation for production use, it is suggested that the Kubernetes documentation be extended with more
detailed information on how to run and maintain Open edX in such an environment, and that Kubernetes support be made a
first-class citizen.

Note that it is perfectly possible to bypass Tutor entirely when it comes to cluster management. One could use its image
creation engine to build the requisite Docker images, then manage the Kubernetes cluster with something like Helm: there
are reports of `this exact thing being done
<https://discuss.openedx.org/t/lets-talk-about-the-native-installation/3269/2>`__).


Criterion 2. Automated deployment and testing
=============================================

Launching a preconfigured instance from scratch is simple, as Tutor can either `load configuration
<https://docs.tutor.overhang.io/configuration.html#configuration>`__ from environment variables, from locally stored
YAML files, or via command line parameters. Automating a launch is therefore just as simple as launching an
``edx/configuration`` Ansible run.

Nevertheless, it is not currently possible to run edx-platform tests in a Tutor-created container, so automation for
this particular purpose will require work.


Criterion 3. Support for the latest open release
================================================

Tutor supports the latest Open edX release, Koa, out of the box.  Furthermore, for at least the past two releases,
development has been done concomitantly with the actual Open edX release itself.

There's also built-in support for upgrading single servers, either `automatically
<https://docs.tutor.overhang.io/local.html?highlight=upgrade#upgrading-from-earlier-versions>`__ or `manually
<https://github.com/overhangio/tutor/blob/master/tutor/commands/local.py#L91>`__.  This, however, `does not apply to
Kubernetes support <https://github.com/overhangio/tutor/blob/master/tutor/commands/k8s.py#L281-L288>`__.  While the
corresponding single-server upgrade procedure can be used as an authoritative reference for how to achieve it manually
on multi-server deployments, ideally there would be specific documentation on it.

It is important to note that Tutor does not share any code with ``edx/configuration``, nor was it built to update
containers in place with tools such as Ansible. As such, like other modern container deployment solutions, there is no
way to update code other than first updating the container image then rolling it out.


Criterion 4. Support for master
===============================

Regis Behmo `reports <https://discuss.openedx.org/t/lets-talk-about-the-native-installation/3269/5>`__ that some work in
this direction has already `been done <https://github.com/overhangio/tutor/tree/edge>`__, but at the time of writing it
is a couple of months behind the latest version of Tutor.

This is clearly an area where contributions are needed. It will likely require one or more volunteers to help maintain
master and fix breakages, as those would inevitably happen. Continuous deployment and some level of integration testing
would also be required, as the volunteer(s) would need to be warned as soon as possible about failures.

Close cooperation with the several engineering teams at edX will be needed: it would be best to have advance warning of
backward-incompatible changes.


Criterion 5. Customization
==========================

Tutor allows not only using `a custom Docker registry and images
<https://docs.tutor.overhang.io/configuration.html#custom-images>`__, but also `building custom images
<https://docs.tutor.overhang.io/configuration.html#custom-open-edx-docker-image>`__.  The latter is how one adds custom
themes, xblocks, or even uses `a fork of edx-platform
<https://docs.tutor.overhang.io/configuration.html#running-a-fork-of-edx-platform>`__.  In operational terms it is no
harder than how one would accomplish the same with ``edx/configuration``.

For many things that Tutor doesn't explicitly support (and for which there is no desire/need to include in the core), it
is possible to `write plugins <https://docs.tutor.overhang.io/plugins/gettingstarted.html>`__.  With this engine, one
can add additional services and configure the rest of the platform to use them. One can imagine writing plugins that
allow deployment of the different Open edX MFEs, for instance.

A note on theming: it would be ideal if it were possible to store static assets separately so that a single base image
could be used for multiple instances, but that is not currently the case.


Criterion 6. Migration from the native installation
===================================================

There is no easily found documentation on migrating to a Tutor-managed installation from a native one, aside from `this
discussion in the Overhang.io forums <https://discuss.overhang.io/t/migrating-from-bitnami-to-tutor/31>`__.

Assuming the source installation is running the same release as the target one, it would be a reasonably straightforward
matter of launching a new Tutor stack (with all the desired customization applied, of course), dumping the state of the
databases, then importing them.  Nevertheless, the exact steps need to be performed, tested, and written down.


Criterion 7. Open edX development environment
=============================================

There is `specific documentation <https://docs.tutor.overhang.io/dev.html#development>`__ on using Tutor as an
Open edX development environment.

Support for master
------------------

Like the production version, Tutor does not currently support running the ``master`` branch of ``edx-platform`` for
development.

Setup procedure
---------------

To achieve a similar setup to the official devstack's, where the LMS, CMS and workers run in detached containers from a
locally checked out ``edx-platform``, and the code of the devstack itself (i.e, Tutor) is also locally modifiable, you
must:

1. Create a self-contained workspace for the devstack, check out Tutor and install requirements:

   .. code-block::

      export WORKSPACE=~/workspace
      mkdir -p $WORKSPACE
      cd $WORKSPACE
      git clone git@github.com:overhangio/tutor.git
      cd tutor
      pip install -e .

2. Follow Tutor's instructions to prepare the default development environment, setting ``TUTOR_ROOT`` so configuration
   is stored independently, and making sure to answer "no" when asked if this is a production environment:

   .. code-block::

      export TUTOR_ROOT=${WORKSPACE}/tutor_root
      tutor local quickstart
      tutor local stop
      tutor images build --no-cache openedx-dev

3. Because the master branch of ``edx-platform`` is not supported at the time of writing, one must clone Open edX at the
   latest supported release *tag*:

   .. code-block::

      cd $WORKSPACE
      git clone -b open-release/koa.1 git@github.com:edx/edx-platform.git

   .. note::

      Note that branches based off of ``master``, or even ``koa.master``, will not work in production.  As noted
      `in the documentation <https://docs.tutor.overhang.io/configuration.html#running-a-fork-of-edx-platform>`__, "Tutor
      will attempt to apply security and bug fix patches that might already be included", and possibly fail.

4. Create a docker-compose override file for the development environment:

   .. code-block::

      vim ${TUTOR_ROOT}/env/dev/docker-compose.override.yml

   And configure it so that your local checkout of ``edx-platform`` is bind-mounted in the correct directory by all
   relevant containers:

   .. code-block::

      version: "3.7"
      services:
        lms:
          volumes:
            - ${WORKSPACE}/edx-platform/:/openedx/edx-platform
        cms:
          volumes:
            - ${WORKSPACE}/edx-platform/:/openedx/edx-platform
        lms-worker:
          volumes:
            - ${WORKSPACE}/edx-platform/:/openedx/edx-platform
        cms-worker:
          volumes:
            - ${WORKSPACE}/edx-platform/:/openedx/edx-platform

5. Prepare the ``edx-platform`` repository inside the LMS container:

   .. code-block::

      tutor dev run lms bash
      pip install -r requirements/edx/development.txt
      npm install
      openedx-assets build --env=dev
      exit

6. You'll need to init the dev environment.  (This is taken care of by ``tutor local quickstart`` for the local
   production environment, and even though the same databases are used for the development environment, apparently the
   ``GRANT`` command needs to be run again when MySQL runs on a different container.)

   .. code-block::

      tutor dev init

7. And finally, to start the LMS, CMS, and workers in the background:

   .. code-block::

      tutor dev start -d

Code and asset reloading
------------------------

When ``edx-platform`` is mounted as described above, changes made to python files are reloaded immediately.

Template and asset changes are also available immediately, but a browser refresh may be necessary.

Debugging and testing
---------------------

It is possible to `run an interactive debugger <https://docs.tutor.overhang.io/dev.html#debug-edx-platform>`__, as well
as `running unit tests locally <https://docs.tutor.overhang.io/dev.html#running-edx-platform-unit-tests>`__.

.. note::

   As noted in the Tutor documentation and elsewhere in this ADR, a few (35 out of roughly 17,000) ``edx-platform``
   tests are currently failing.  This is known issue, though these same tests are reported to also fail when run in the
   official devstack.

Custom development
------------------

The development environment supports installation and development of `custom python packages
<https://docs.tutor.overhang.io/dev.html#xblock-and-edx-platform-plugin-development>`__ (such as XBlocks or
``edx-platform`` plugins), as well as `custom themes <https://docs.tutor.overhang.io/dev.html#customised-themes>`__.

Criterion 8. Project health
===========================

Tutor as an open source project is `3 and a half years old
<https://github.com/overhangio/tutor/commit/bdd1a41f6207275bd24c98136fc567af2ea48b7f>`__.  It saw a peak of activity `in
2019 <https://github.com/overhangio/tutor/graphs/code-frequency>`__, and has since then maintained a steady pace of
improvement. The company behind it, `Overhang.IO <https://overhang.io/>`__, is declaredly committed to open source
values and offers `Tutor deployment <https://overhang.io/tutor/>`__ as a service, and thus have a financial incentive to
continue maintaining it.

It is, however, still effectively maintained `by a single developer
<https://github.com/overhangio/tutor/graphs/contributors>`__: Regis Behmo. This poses a significant risk: if for some
unforeseen reason Regis can no longer maintain Tutor, the Open edX community would again be orphaned. Thus, it is
suggested that some form of co-governance/co-maintenance be instituted. Regis has `tentatively agreed
<https://discuss.openedx.org/t/lets-talk-about-the-native-installation/3269/5>`__.  Hopefully, with more people and
companies using Tutor, more contributors will be willing to take on this responsibility.

Tutor's codebase is technically up-to-date, well organized, and maintained. There is no detectable technical debt.


Conclusion of the evaluation
============================


Positives
---------

At the time of writing, Tutor is the only existing alternative to ``edx/configuration`` with a maintainer that is active
in the community. It ticks a lot of the boxes that qualify a good replacement:

-  It can do single-server production deployments well
-  It allows for automated deployment as well as, or better than, ``edx/configuration``
-  It supports Koa, and new releases are added quickly
-  It allows for theme customization, running edx-platform forks, and installing custom requirements
-  It is extensible via plugins
-  It can be used as a lightweight, efficient Open edX development environment
-  Actively maintained by a committed developer; codebase has no technical debt
-  Licensed under the AGPL v3


Omissions
---------

There is work to be done before it can be considered a full replacement:

-  There must be a documented way to run highly-available, multi-server production deployments, including a way to run
   rolling upgrades with as little downtime as possible (see note below)
-  Migration from the native installation must be documented
-  It must support deployments of the master branches of edx-platform and IDAs, and once that comes about it must
   undergo CI/CD so that breakages can be dealt with quickly
-  There should be more than one maintainer

.. note::
   The nature of Open Releases (Juniper, Koa, etc.) make it very difficult to allow for no-downtime rolling upgrades:
   they're a mashup of multiple edx.org releases which over the relevant time period introduce backward-incompatible
   migrations that can't be easily conflated.  It should, however, be possible to do if one is tracking master closely
   enough.

It is estimated that initial versions of the above will take anywhere from three to six months to achieve, if sufficient
developers commit resources to the endeavor. (Part of the test of whether a community-supported deployment method will
work hinges precisely on whether this community can pool resources to maintain it!)


Wishlist
--------

The following would be great to have once the TODO list above is taken care of:

-  Support the deployment of most MFEs
-  There should be a way to reuse the same base LMS image with different themes without having to bake them into the
   image
-  An easier-to-set-up devstack-like local development environment


Decisions
*********

1. Adopt Tutor as the community-maintained replacement for ``edx/configuration`` for production deployments, with the
   knowledge that it needs to be `further developed <#omissions>`__ in order to achieve all the described `criteria
   <#criteria>`__.

2. For the following release, Lilac, ``edx/configuration`` will be supported but marked as deprecated, and Tutor will be
   supported for the first time.

3. For the release after Lilac, Maple, ``edx/configuration`` will be no longer supported, and Tutor be the only
   supported installation method.

The above decisions will be made public in order to garner support for code contributions.


Alternatives
************

These are open source Open edX deployment projects that also aim to replace ``edx/configuration``:

-  `Open edX Docker <https://github.com/openfun/openedx-docker>`__: maintained by France Université Numérique, aims to
   provide a Dockerfile that installs a complete Open edX.  Used in production with OpenShift.
-  `Derex <https://github.com/Abstract-Tech/derex.runner/>`__: partly based on Tutor, also uses Dockerfiles and
   ``docker-compose`` to launch Open edX instances.

And it is worth mentioning that:

-  edX itself is moving to `Kubernetes with Helm
   <https://openedx.atlassian.net/wiki/spaces/AC/pages/2107441855/Braindump+on+Configuration+Today+and+Future#Future-with-Containers>`__,
   but at this time do not intend to make their Helm charts public - though they're open to the idea of publishing and
   maintaining sample versions.


Appendix
********

Why are Tutor deployments so much quicker?
==========================================

The default Tutor deployment is multiple times faster than an ``edx/configuration`` run primarily because it sticks to
the concept of `container immutability
<https://cloud.google.com/solutions/best-practices-for-operating-containers#immutability>`__.  This means that when one
runs ``tutor local quickstart``, no static assets are gathered, and there is otherwise nothing to be done to the base
images: deployment mostly hinges around how connection speeds will affect image downloads.

If, however, one wants to customize the Open edX image, it needs to be rebuilt locally. That makes the full run, which
includes `cloning of the edx-platform repo
<https://github.com/overhangio/tutor/blob/a17c8dab2409bbb5821c6bc32a21b3a83ba4d769/tutor/templates/build/openedx/Dockerfile#L30>`__,
`installation of Python requirements
<https://github.com/overhangio/tutor/blob/a17c8dab2409bbb5821c6bc32a21b3a83ba4d769/tutor/templates/build/openedx/Dockerfile#L65>`__,
and static asset gathering, more comparable to the Open edX devstack run. Regis reports having gone to `great lengths
<https://discuss.openedx.org/t/lets-talk-about-the-native-installation/3269/5>`__ to optimize the `asset gathering
process
<https://github.com/overhangio/tutor/blob/a17c8dab2409bbb5821c6bc32a21b3a83ba4d769/tutor/templates/build/openedx/Dockerfile#L166-L172>`__.
A full image rebuild took 32 minutes.
