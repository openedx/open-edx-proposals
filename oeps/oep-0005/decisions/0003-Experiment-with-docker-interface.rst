Experiment with docker interace
===============================


Status
------

Accepted


Context
-------

.. note:: This ADR's scope is very limited to developer facing make interface for docker and does not address questions on other make interfaces.

.. note:: This decision has a shelf life of the enterprise-catalog prototype and will be reevaluated as we continue testing decentralized devstack.

The current devstack wraps some common docker-compose commands with make targets.

During development, the question about the necessitate of make targets to wrap common docker-compose files was raised. Some make target seemed unnecessary as wrappers to simple commands.

Some of the make targets that are deemed unnecessary:

- build: docker-compose build
- pull: docker-compose pull
- down: docker-compose down
- shell: docker-compose exec app bash
- logs: docker-compose logs -f app
- up: docker-compose up -d

Options
~~~~~~~

1. Follow the pattern of current Devstack, which uses make targets as a basic interface for developers.

2. Use docker-compose commands directly for managing images and containers


Decision
--------

For initial prototype, we will attempt to interface with DD through docker-compose commands directly. How_to docs should be created to help developers use docker-compose commands.

Main reasons for decision:

- these make targets do not sufficiently decrease verbosity
- makes debugging easier by allowing you to directly use resources on the interwebs
- standardization by using external standard
- developers will learn how to use this essential technology during normal work rather than have to learn it only during debugging


This decision will be reevaluated after prototyping with enterprise-catalog.
