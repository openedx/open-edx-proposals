Data Generation Functions
=========================

Status
------

Accepted

Context
-------

Devstack currently has no standard way of creating dev data. Over time, developers have figured out their own ways to create data for local development. Recently, `OEP-37`_ was proposed, which suggests a standardized method for creating dev data.

Decision
--------

To be iterative in implementation, we decided not to follow `OEP-37`_ completely. Specifically, we decided to implement dev data generation functions in the `load_dev_data` management command. This results in centralization of dev data creation into the `load_dev_data` management command. This is meant to be a temporary measure until we have wider usage of this method of creating data.

In the future, we hope to move these dev data generation functions into aplugin style archetecture, where `load_dev_data` management command is able to discover dev data generation functions during run time.

Consequences
------------

- Dev data generation is centralized to `load_dev_data`management command in each IDA
- The file will grow larger as new data is supported
- `load_dev_data` management command will be modified everytime new data is supported



.. _OEP-37: https://github.com/edx/open-edx-proposals/pull/118
