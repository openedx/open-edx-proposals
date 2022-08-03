Centralization of Development Data Yaml files
#############################################

Status
******

Accepted

Context
*******

Devstack currently has no standard way of creating dev data. Over time, developers have figured out their own ways to create data for local development. Recently, `OEP-37`_ was proposed, which suggests a standardized method for creating dev data. We decided the proposal was good enough to warrant a prototype and during the prototyping phase, we ran into the question of how much of this method we want to centralize into devstack, vs keep things decentralized by keeping as much as is possible in each IDAs.

Specifically, this ADR pertains to question of where to put yaml files specifiying what data to load into the devstack development environment. As it is implemented, each IDA will have its own yaml files which lay out what data to create for that IDA.


Decision
********

Since this method is meant to be used in devstack to orchestrate dev data across multiple IDAs, we decided to put all of the data yaml files into the devstack repo. This decision is especially relavant for yaml files specifiying what data to create during provisioning and for very specific common workflows. The hope is that developers will only have to modify yaml files in devstack repo to create more custom data.

Clarification, this decision does not restrict yaml files existing in each individual IDA's repo.

Consequence
***********

This will result in yaml files being farther away for the related code that uses those files to create data, i.e models and data creation functions.

Rejected Alternatives
*********************

Put Data yaml files in each individual IDA's repo, this is rejected cause this would make it harder for developers to know what is being created if they have to go look at yaml files in each IDA.

.. _OEP-37: https://github.com/edx/open-edx-proposals/pull/118
