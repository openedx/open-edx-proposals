Adding lms data dump
####################

Status
******

Accepted

Context
*******

One of the goals for DD is to minimize start time/setup time. Setting up the database is one of the major bottlenecks.

Since this is the beginning of DD, the methods of providing necessary data to a general service's DD has not been established. A generalized solution has been proposed in `OEP-37`_, but it does not yet exist.

.. _OEP-37: https://github.com/openedx/openedx-proposals/pull/118


Decision
********

In lieu of general method to provide data and in need to develop and test quickly, we've decided to place necessary lms mongo and sql data dumps in `/decentralized_devstack/data_dump` directory of the repository alongside the application code. The hope is to use this temporary data dump for testing and if this method proves promising, develop a more general solution for providing data.

Consequences
************

The lms data dump will live in enterprise-catalog git repository.
