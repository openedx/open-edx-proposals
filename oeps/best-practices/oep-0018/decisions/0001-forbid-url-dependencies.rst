1. Forbid VCS URL Python Dependencies
#####################################

Status
******

Accepted

Follow-up work:

* https://github.com/openedx/public-engineering/issues/162
* https://github.com/openedx/public-engineering/issues/163


Context
*******

Former Guidance
===============

``pip`` allows dependencies to be installed via VCS (version control system,
e.g. git) URL, for example::

    git+https://github.com/edx/edx-ora2.git@2.1.15#egg=ora2==2.1.15

OEP-18 guidance on this was previously:

  ... you should generally avoid installing requirements from a URL
  or local directory instead of PyPI.  But there are a few circumstances where
  it can be appropriate:

  * You need to test a release candidate of the dependency to make sure it will
    work with your code.
  * You critically need a fix for a package which has not yet been included in
    a release, and you cannot arrange for a release to be made in a timely
    manner.

  In most other circumstances, the package should be added to PyPI instead.  If
  you do need to include a package at a URL, it should have both the package
  name and version specified (end with "#egg=NAME==VERSION"). ...

Issues with Former Guidance
===========================

Performance
-----------

Firstly, installing from VCS URL is significantly slower than installing from PyPI.
We understand this in theory, based on the facts that:

* ``pip`` must download and build a repository's entire contents when freshly installing
  via VCS URL, whereas PyPI-hosted packages can be downloaded as pre-built wheels; and
* when re-installing a dependencies, ``pip`` must fully re-install VCS-hosted
  packages, whereas it will skip over any PyPI-hosted package whose version number has not
  changed.

We confirmed our theoretical understanding with a `cursory performance comparison`_.
It demonstrated that installing a package into a fresh system from a VCS URL took
nearly twice as a long as installing the same package from PyPI. It also
demonstrated that installing an already-installed dependency from a VCS URL
took four times as long as doing so for a package installed via PyPI.

Upgrades
--------

OEP-18 describes a format for PyPI-hosted dependencies that allows for
automated upgrades to be performed using the ``make uprade`` Makefile target.
This target upgrades all version pins to the latest version, as allowed by the
version restrictions specified in ``constraints.txt``.

VCS URL dependencies work completely outside of this system. Each VCS URL
is essentially a version pin, which cannot be upgraded by automated tooling.
This runs counter to the "upgrade pins by default" philosophy of OEP-18.
edx-platform's ``github.in`` file has VCS URL pins that have not been
updated for over three years.

Lack of reason to allow VCS URLs
--------------------------------

As noted above, OEP-18 carved out two exceptions in which VCS URLs were
acceptable. Firstly:

    You need to test a release candidate of the dependency to make sure it will
    work with your code.

This is a valid thing to test, but it should be done locally or in some sort of
sandbox using a feature branch. A VCS URL should never be merged to main or master
for testing purposes.

Secondly:

    You critically need a fix for a package which has not yet been included in
    a release, and you cannot arrange for a release to be made in a timely
    manner.

Generally, fixes can be achieved by reverting to a previous, stable package version.
In the rare event that reverting to a previous package version is not viable,
Open edX repository maintainers can easily cut new releases of packages in order
to accomodate a forward fix. If a repository maintainer is unresponsive, this
issue can be escalated to tCRIL, which can cut an emergency release if necessary. If
the repository is outside of the Open edX project, then the repository can be forked, and
the package re-released to PyPI under a new name.

Finally, even if those reasons were valid, they are *not* the reasons behind
`the VCS URL dependencies installed in to edx-platform as of Nutmeg
<https://github.com/openedx/edx-platform/blob/open-release/nutmeg.2/requirements/edx/github.in#L54-L77>`_
Following ``git blame`` shows that the current VCS URL dependencies
exist simply because it took fewer steps to set them up than it would have to
install them from PyPI.

Decision
********

We forbid the use of VCS URLs for installing Python dependencies into
Open edX projects.

Consequences
************

* OEP-18 is updated with the new guidance and a link to this ADR.
* We will `remove edx-platform's GitHub dependency file <https://github.com/openedx/public-engineering/issues/162>`_.
  This will involve publishing all packages listed there to PyPI
  and then re-specifying the existing VCS URL dependencies as PyPI dependencies.
* We will `create a dependency linter workflow <https://github.com/openedx/public-engineering/issues/163>`_
  to help ensure that only PyPI-based dependencies, as specified in OEP-18, are used in Open edX
  repositories. The linter workflow will be added to
  `the common set of workflows <https://github.com/openedx/.github/tree/master/.github/workflows>`_
  with a `shared workflow template <https://github.com/openedx/.github/tree/master/workflow-templates>`_ available.
* The new linter will be added to the
  `edx-cookiecutters Python template <https://github.com/openedx/edx-cookiecutters>`_ and edx-platform.
  Maintainers will be encouraged to add the workflow to their own repositories.

Appendix
********

.. _cursory performance comparison:

PyPI vs. VCS Performance Comparison
===================================

We performed a non-scientific comparison between the performance of four ``pip`` test commands,
two of which installed a PyPI-based dependency and two of which installed a VCS URL
depdendency. For each test command, we performed ten trials.

One 'trial' consisted of running a test command in a bash shell within the standard Python Docker container, as so::

  docker run -it python:latest bash -c "$TEST_COMMAND"

Each test command invoked the Unix ``time COMMAND ARGS...`` command, which reports the amount
of wall-clock time it took for ``COMMAND ARGS...`` complete. For example, the command::

     pip install edx-codejail==3.2.0 && time pip install edx-codejail==3.2.0

reports the amount of time it took for the *second* invocation of ``pip install edx-codejail==3.2.0``
to run from start run from start to finish.

Testing was perfomed on a Thinkpad X1 Carbon Generation 8 using Docker version 20.10.18. 
The latest Python Docker container as of 2022-10-04 was used. The ``edx-codejail`` package
was chosen because it is a moderately-sized Python package and, at time of writing,
is a VCS URL dependency of edx-platform's.

The results for each test command are shown below.

.. list-table::
   
   * - **Source**
     - PyPI
   * - **Action**
     - Fresh package install
   * - **Test Command**
     - ``time pip install edx-codejail==3.2.0``
   * - Trial 1
     - 1689 ms
   * - Trial 2
     - 1772 ms
   * - Trial 3
     - 1604 ms
   * - Trial 4
     - 1595 ms
   * - Trial 5
     - 1662 ms
   * - Trial 6
     - 1650 ms
   * - Trial 7
     - 1665 ms
   * - Trial 8
     - 1576 ms
   * - Trial 9
     - 1705 ms
   * - Trial 10
     - 1608 ms
   * - **Average**
     - **1653ms**

.. list-table::

   * - **Source**
     - PyPI
   * - **Action**
     - Re-installing after fresh install
   * - **Test Command**
     - ``pip install edx-codejail==3.2.0 && time pip install edx-codejail==3.2.0`` 
   * - Trial 1
     - 0372 ms
   * - Trial 2
     - 0370 ms
   * - Trial 3
     - 0380 ms
   * - Trial 4
     - 0372 ms
   * - Trial 5
     - 0377 ms
   * - Trial 6
     - 0378 ms
   * - Trial 7
     - 0373 ms
   * - Trial 8
     - 0373 ms
   * - Trial 9
     - 0371 ms
   * - Trial 10
     - 0367 ms
   * - **Average**
     - **0373 ms**

.. list-table::

   * - **Source**
     - VCS URL
   * - **Action**
     - Fresh package install
   * - **Test Command**
     - ``time pip install git+https://github.com/openedx/codejail#egg=edx-codejail==3.2.0`` 
   * - Trial 1
     - 3112 ms
   * - Trial 2
     - 3074 ms
   * - Trial 3
     - 3411 ms
   * - Trial 4
     - 3058 ms
   * - Trial 5
     - 4155 ms
   * - Trial 6
     - 3507 ms
   * - Trial 7
     - 3042 ms
   * - Trial 8
     - 3158 ms
   * - Trial 9
     - 3180 ms
   * - Trial 10
     - 3069 ms
   * - **Average**
     - **3277 ms**

.. list-table::

   * - **Source**
     - VCS URL
   * - **Action**
     - Re-installing after fresh install
   * - **Test Command**
     - ``pip install git+https://github.com/openedx/codejail#egg=edx-codejail==3.2.0 && time pip install git+https://github.com/openedx/codejail#egg=edx-codejail==3.2.0`` 
   * - Trial 1
     - 1642 ms
   * - Trial 2
     - 1505 ms
   * - Trial 3
     - 1461 ms
   * - Trial 4
     - 1464 ms
   * - Trial 5
     - 1411 ms
   * - Trial 6
     - 1536 ms
   * - Trial 7
     - 1545 ms
   * - Trial 8
     - 1686 ms
   * - Trial 9
     - 1504 ms
   * - Trial 10
     - 1381 ms
   * - **Average**
     - **1514 ms**

