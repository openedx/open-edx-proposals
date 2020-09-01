To Make or Not to Make
======================


Status
------

Accepted


Context
-------

For Decentralized Devstack, an common interface is necessary to enable a OpenedX Developer to add contributions to the many independent services in Open edX codebase.

Options
~~~~~~~

1. The current Devstack uses make targets as an basic interface for developers.

2. Use docker-compose commands directly

Case for and against make targets
`````````````````````````````````

For:

- This standard already exists and is known by developers at edx.

  * If we don't create a make interface now, sevices might create their on make interface, which might/will result in non-standard make experience
- make targets decrease verbosity and memorization

Against:

- make targets do not provide much value

  * most targets wrap commands that are/can be only few characters longer

- by wrapping, make targets mask what is happening, thus adding another layer to disentangle during debugging
- comes with maintanence costs

Case for and against docker-compose commands
````````````````````````````````````````````

For:

- external industry standard maintained by vibrant community
- by working directly with docker-compose commands, it enforces better structure of Dockefile and docker-compose.yml
- developers will learn how to use this essential technology during normal work rather than have to learn it only during debugging

Against:

- more verbose than make targets

  * if it becomes impossible to keep Dockefile and docker-compose.yml simple, either how_to docs will become super confusing or services will create their own interface
- another tool for developers to learn. Increase in learning curve.

Decision
--------

The case for moving away from make targets and using docker-compose is stronger.

For initial protype, we will attempt to interface with DD through docker-compose commands directly. How_to docs should be created to help developers use dockerk-compose commands.

This decision will be reevaluated after prototyping with enterprise-catalog.
