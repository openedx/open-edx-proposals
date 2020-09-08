To Make or Not to Make
======================


Status
------

Accepted


Context
-------

For Decentralized Devstack, a common interface for is necessary to enable an OpenedX Developer to add contributions to the many back-end services in Open edX codebase.

.. Note:: This ADR's scope is very limited to developer facing make interface for docker and does not address questions on other make interfaces.

.. Note:: This decision has a shelf life of the enterprise-catalog prototype and will be reevaluated as we continue testing decentralized devstack.

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

1. Follow the pattern of current Devstack, which uses make targets as an basic interface for developers.

2. Use docker-compose commands directly

Case for and against make targets
`````````````````````````````````

For:

- This standard already exists and is known by OpenedX developers.

  * If we don't create a make interface now, services might create their on make interface, which might/will result in non-standard make experience

- make targets decrease verbosity and memorization
- Can be used to enable a UI across projects based on different tech stacks
- Provide a "canonical example" of how to do those operations

Against:

- these make targets do not sufficiently decrease verbosity

  * most targets wrap commands that are/can be only few characters longer

- by wrapping, make targets mask what is happening, thus adding another layer to disentangle during debugging
- comes with maintenance costs

Case for and against docker-compose commands
````````````````````````````````````````````

For:

- external industry standard maintained by vibrant community

  * By using an external(non OpenedX) interface, we automatically get some consistency across projects

- by working directly with docker-compose commands, it enforces better structure of Dockerfile and docker-compose.yml
- developers will learn how to use this essential technology during normal work rather than have to learn it only during debugging

Against:

- more verbose than make targets

  * if it becomes impossible to keep Dockerfile and docker-compose.yml simple, either how_to docs will become super confusing or services will create their own interface

- another tool for developers to learn. Increase in learning curve.

Decision
--------

The case for moving away from make interface for docker and using docker-compose is stronger.

Main reasons for decision:

- these make targets do not sufficiently decrease verbosity
- using an external standard, this accomplishes few things:

  * makes debugging easier by allowing you to directly use resources on the interwebs
  * standardization by using external standard


For initial prototype, we will attempt to interface with DD through docker-compose commands directly. How_to docs should be created to help developers use docker-compose commands.

This decision will be reevaluated after prototyping with enterprise-catalog.
