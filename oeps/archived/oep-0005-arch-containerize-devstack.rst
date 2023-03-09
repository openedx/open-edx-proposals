OEP-5: Pre-built Developer Environments
#######################################

+---------------+----------------------------------------------------+
| OEP           | :doc:`OEP-5 <oep-0005-arch-containerize-devstack>` |
+---------------+----------------------------------------------------+
| Title         | Pre-built Developer Environments                   |
+---------------+----------------------------------------------------+
| Last Modified | 2016-08-04                                         |
+---------------+----------------------------------------------------+
| Author        | Calen Pennington <cale@edx.org>                    |
+---------------+----------------------------------------------------+
| Arbiter       | Jesse Zoldak <jzoldak@edx.org>                     |
+---------------+----------------------------------------------------+
| Status        | Replaced                                           |
+---------------+----------------------------------------------------+
| Type          | Architecture                                       |
+---------------+----------------------------------------------------+
| Created       | 2016-06-28                                         |
+---------------+----------------------------------------------------+

.. attention::

   This OEP has been replaced by
   :doc:`/architectural-decisions/oep-0045/decisions/0001-tutor-as-replacement-for-edx-configuration`
   a decision made as a part of :doc:`/architectural-decisions/oep-0045-arch-ops-and-config`

Abstract
********

Open edX is a large ecosystem of smaller services. In order to facilitate
newcomers working in that system, each service will provide a pre-built
environment that is packaged, published, and ready to be used for development.

Motivation
**********

Open edX and the edx-platform repository have gone through several iterations
of developer setup instructions.

The first set of instructions detailed how to install edx-platform and all of
its dependencies on your local workstation. As the number of developers and
developer environments increased, so did the workload of maintaining
instructions to work around all of the idiosyncrasies in those environments.
These instructions weren't useful for production installations, which were
standardized on a particular release of the Ubuntu operating system.

The second, current, method of development is referred to as ``Devstack``. This
is a `Vagrant`_ image that comes prepackaged with installations of
edx-platform, forums, and an increasingly large number of other services, as
well as single shared instances of the databases that they all need to run.
This marked a significant improvement over the ad-hoc installation methods,
because it was able to use the infrastructure that was built out for installing
Open edX in production.  However, there are several problems with the current
monolithic Vagrant image which we would like to address:

1. As new services are added to the Open edX ecosystem, the Vagrant image
   grows in size, requiring more system memory from the host machine.

2. Vagrant imposes noticeable overhead during file system access, which
   especially hurts any development that is doing file-heavy operations (such
   as manipulating static assets).

3. The monolithic Vagrant image isn't particularly representative of the
   production service architecture, because all of the services are installed
   on the same machine, which makes it harder for developers to be confident
   that their changes will work when deployed.

4. The shared services dependencies on things like MySQL and Elasticsearch mean
   that all of the services in Devstack have to be upgraded at the same time,
   or have to maintain compatibility with multiple database versions until all
   services are ready to update. This hinders independent development on the
   services.

5. Because all of the services are installed on the same image, the image
   either has to be updated by every user after downloading (to update to the
   latest code versions), or the image has to be rebuilt every time any of the
   installed services change (which results in lots of churn).

6. Because the Vagrant image contains many separate services, the definition
   of that image has to be separate from any of the service repositories, which
   makes it hard to update (and to push updates out to users).


Specification
*************

Interface
=========

The primary developer interaction with the local development environment will
be through ``make`` shortcuts in the repository containing the code for the
service being developed.  For example, in order to work on an ``edx-platform``
local development environment, a developer could run ``make dev.watch`` from a
checked-out `edx-platform`_ repository.

The following targets will be standard::

    make dev.watch  # Start the local development environment services (if
                    # they aren't already) and display their logs on stdout.

    make dev.shell  # Start the local development environment services (if they
                    # aren't already) and access a shell on the primary
                    # container (as the user that owns the service, in the
                    # directory that has the service source code).

    make dev.clean  # Stop the local development environment services and
                    # remove any intermediate data that they stored in the
                    # .dev/ directory.

    make dev.*      # Start the local development environment services (if they
                    # aren't already) and run `make *` from the code directory
                    # of the primary service (as the service owner).

Implementation
==============

Requirements
------------

1. Third-party services like MySQL and MongoDB should be run using vendor
   provided official containers. If no reasonable alternative exists to
   customizing a provided container, the minimal changes should be applied as a
   layer on the provided container.

   **Rationale**: We should be aiming to minimize the amount of work needed to
   support our development environments. Minimizing changes to third-party
   containers means we have less code/configuration that we need to update as
   third-party containers and applications change. Minimizing customization
   should also push us in the direction of making it easy to deploy our
   stack, because there will be fewer customizations needed by operators of
   Open edX in production.

2. Open edX containers must run all of their processes via `supervisor`_ to
   provide a single entry point even when the container has multiple processes
   (such as `nginx`_ and a `Django`_ service running under `gunicorn`_).

   **Rationale**: `Docker`_ imposes a single-entry-point constraint.
   Standardizing on supervisor means that we need to make fewer choices
   during development, and that we can combine the learnings and
   configurations across various Open edX projects.

3. Open edX services running in containers must log to ``stdout`` and
   ``stderr``.

   **Rationale**: Logging to ``stdout`` and ``stderr`` makes it significantly
   easier to develop the app in different environments, since those file handles
   are available nearly everywhere. Supervisor captures both file handles and
   logs their contents to disk, which means that we can display the logs to the
   screen, and can search through old development logs.

4. Dependent services (such as databases) should be configured by default to
   mount their storage directories inside ``.dev/`` inside the source code
   directory (this should be added to `.gitignore`_ and `.dockerignore`_). This
   location should be configurable via an environment variable.

   **Rationale**: This will allow data from local development environment to
   persist across container executions. Locating the storage in the source code
   repository isolates the storage of third-party containers that are used in
   common between local development environments so that data doesn't leak
   between them.  Making the storage location configurable means that local
   development environments could share storage if desired.

5. The Open edX service must mount the source code directory from the host
   machine into the development container so that the local development
   environment is always running the version of code being edited.

   **Rationale**: The development container should always run the latest
   source code that the developer is editing. Having it mount the local source
   code means that the developer can edit on their local machine and have their
   changes reflected immediately in the container.

6. The local development environment should be configured to auto-reload when
   source code is modified.

   **Rationale**: Fast feedback loops are key to developer productivity. Having
   the application reflect changes made by the developer as soon as possible
   allows the developer to test and validate their changes quickly.

7. Open edX Service containers should be built during CI, and published so that
   any developer can download the latest container. They should also be tagged
   for each versioned release.

   **Rationale**: Having a container that is up-to-date with both code and
   configuration makes it easier for developers to make their changes with
   confidence that those same changes will work once they are merged. Having
   containers for previous releases allows easier development for hot fixes.

8. The local development environment should be installed with all
   pre-requisites to allow tests to be run on them without additional developer
   input.

   **Rationale**: Tests are an important part of the fast feedback loop
   for developers. Being able to run those tests immediately when the local
   development environment starts means that developers can have confidence
   that the environment is working correctly for development.

9. Development containers should be designed for development over production.

   **Rationale**: The local development environment is intended to make it easy
   for new and existing developers to make changes to Open edX services. As
   such, they should be focussed first on development, and only secondarily on
   production-readiness or production-fidelity.

Technology Selection
--------------------

The prototype implementation was done using Docker for the containers, and
`Docker Compose`_ to manage the networking between services.

Rationale
*********

Docker has a low per-service overhead, because it doesn't allocate resources
to particular containers.

Docker uses a layered file system, which, if used properly, should let us
limit the amount of data that a developer needs to download to update from
one version of the base image to the next.


Backward Compatibility
**********************

This implementation supersedes and replaces use of Vagrant for the local
development environment.  However, nothing in the implementation should break
the Vagrant-based local development environment during the development
phase, and developers currently using Vagrant will be able to continue to
use it. Long-term, the goal will be to deprecate and no longer support the
Vagrant-based local development environment.

Reference Implementation
************************

The `course-discovery`_ service uses a Docker-based local development
environment, and was the prototype implementation of this OEP. However, it
will need modification to fully meet the specifications of this OEP.


Related Decisions
*****************

The following related decisions modify or enhance this OEP, but have not yet been fully incorporated as updates to this OEP:

.. toctree::
   :caption: OEP-5 Decisions
   :maxdepth: 1
   :glob:

   oep-0005/decisions/*


Change History
**************

.. _.dockerignore: https://docs.docker.com/engine/reference/builder/#dockerignore-file
.. _.gitignore: https://git-scm.com/docs/gitignore
.. _Django: https://www.djangoproject.com/
.. _Docker Compose: https://docs.docker.com/compose/overview/
.. _Docker: https://www.docker.com/
.. _Vagrant: https://www.vagrantup.com/
.. _course-discovery: https://github.com/openedx/course-discovery
.. _edx-platform: https://github.com/openedx/edx-platform
.. _gunicorn: http://gunicorn.org/
.. _nginx: https://www.nginx.com/
.. _supervisor: http://supervisord.org/
