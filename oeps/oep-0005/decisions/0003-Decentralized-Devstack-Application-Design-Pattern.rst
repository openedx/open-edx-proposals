Decentralized Devstack Application Design Pattern
=================================================

Status
------

Draft


Goal for pattern
----------------
- Have a standard way to spin up a local development environment for dockerized applications.
- Use industry standard tools in standard ways.
- Be easy to understand for new developers.

Pattern
-------
- Create Dockerfile for application in-app repository to build the docker image that contains all the requirements to run the application. 
- Create docker-compose.yml file in the application repository to define services, networks, and volumes needed by the application to start.
- Create how_to docs in each service explaining how to get started(these should essentially be the same for each service)

TODO: add more to standard
