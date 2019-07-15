======================
OEP-39: Yaml configuration for django applications
======================


+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-39 </oeps/oep-39-bp-yaml-config>`            |
+-----------------+--------------------------------------------------------+
| Title           | Yaml configuration for django applicatio               |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2019-07-15                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Cory Lee - cory@edx.org                                |
+-----------------+--------------------------------------------------------+
| Arbiter         | TBA                                                    |
+-----------------+--------------------------------------------------------+
| Status          | Draft                                                  |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2019-07-15                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | <start - target end dates for review>                  |
+-----------------+--------------------------------------------------------+
| `Resolution`    |                                                        |
+-----------------+--------------------------------------------------------+
| `References`    |                                                        |
+-----------------+--------------------------------------------------------+

Abstract
========

Yaml configuration instead of python django configuration settings files for configuring django applications is preferable because it simplifies configuration of django applications

Motivation
==========

Currently there is no standard for this and many applications have varying takes on configuration. 
Since Open edX consists of many applications managing and configuring it can be difficult, It would be much easier if all of the applications were configured consistently. 
This OEP intends to provide a set of standard best practices for configuring Open edX in a python django application.

Specification
=============

Your configuration should consist of two files

settings.py
defaults.py 

defaults.py should list every configuration key in the application, and provide a description of what it does.

settings.py should do two things:
1) It should first load defaults.py into the python settings module.
2) Then should load a single yaml file into the default keyspace overwriting values in the settings module.

The yaml file path loaded should be configurable via environment variable that is specific to the application
eg: ECOMMERCE_CFG. 

There should be no additional django configuration settings files like devstack.py, testing.py, docker.py, docker_devstack.py, or anything of the sort.

Plugins should not modify the loaded settings python module. Since we load the keys from the yaml file, even the ones we do not know at the application layer.
A plugin should not need to modify the configuration of the application.

All of the configuration should be done in the yaml file

configuration should never be appended to or removed from in python code after settings.py is loaded, only read.

eg:
```
in
MY_KEY: my_value

becomes settings.MY_KEY in code
```


the default configuration should be production-ready for Open edX.  It should not be a dev configuration. Dev configurations should be implemented by providing a dev yaml file that changes the appropriate settings.
Testing should be done by providing a testing yaml file that changes the appropriate settings.

No additional ENV variables should be needed to be set to run the application.  Only the one that determines where the configuration file lives.


Rationale
=========

Configuration should be Open edX centric because we want it to be as easy as possible for ourselves and for others to run our site, and we want to 'dog food' running our applications.

A production centric configuration should be used over a development centric one because if you push a new default configuration setting to openedx it will ship to people's production sites if they do not overwrite it.  It is better for an unset configuration value to default to a production ready value as development configurations are often more dangerous than production ones when run in the wild.
As a contrived example, it is better to get a TLS error with a new feature when upgrading to the new version of openedx than it is to not encrypt the data.

Furthermore since we provide devstack an example development centric yaml file will be maintained, but we don't control the yaml file people use in their production sites.

The name settings.py is used instead of production.py so that you dont have to remember to specify the django environment everywhere you interface with the application.

defaults.py is chosen because it is semantic and clearly defines its purpose.

The reason no other django settings files should exist is that they frequently duplicate code, and no change is made in there that cannot be represented in the yaml, and having them 
forces you to have to specify --settings= everywhere you interface with the application(management commands nginx, etc.) These files also often modify the config after they are loaded by add/remove/appending to settings when the file is executed.
This interpreting of the configuration at runtime in python makes the configuration harder to reason about and does not provide additional functionality. Since many values must be defined for the application to function these files will often either load other configuration files and then override settings(adding another layer of configuration indirection), or redefine all the settings defined in the other modules, typically only changing a few values and increasing code duplication.  eg. In the platform the plugins are forced to know all of the environments that the application code might have and to provide boilerplate code with magic strings in it to get their configuration loaded.  All of this boilerplate code could be removed using this method.

Having all changes in the yaml file makes it so that all plugin code can simply assume its settings are defined in the yaml file and therefore the python settings module without having to worry about defining configuration for various environments.
This eliminates a lot of boilerplate code for plug in developers.

Starting from the production defaults and making changes for development minimizes the differences in configuration between production and development environments.

ENV variables should not be used to hold secrets as they are often leaked by web frameworks and plug ins in development configurations, and are accessed much easier by an attacker than a file in a variety of scenarios.

As we make more django applications it is easier to maintain them if they are configured the same way.

defaults.py provides a place where one can expect to find documentation about each available setting, if that documentation exists.

Backward Compatibility
======================

This change is not backwards compatible, but it need not be implemented in each application immediately.
It is meant to serve as a set of best practices for moving forward, but

In general the migration path for old applications would look like this:

	For all applications:
    rename production.py to settings.py
    rename base.py or common.py to defaults.py
    make production.py source defaults.py and read yaml into python settings module 
    (it should not specify every key like it does in many apps, it should load keys from the yaml into keys in the settings module without knowledge of what keys are possible.)
    
    Make yaml files for overrides in any other environments and remove the additional python environments.
    Remove any --settings= from places where we interface with the application such as management commands, and from documentation for the application.

    For edx platform specifically:
    edxapp application code would be updated to read all config vars  from 'settings.' instead of settings.AUTH_TOKENS or settings.ENV_TOKENS
    (The settings are currently loaded into both for reverse compatibility)

	plugin developers would delete their settings directories from their plugins and read settings from 'settings.' instead of settings.ENV_TOKENS and settings.AUTH_TOKENS
    the code that passes the settings module into each plugin for modification would be deleted from the platform. (last line or two of most production.py settings files, and the code that it calls.)
    settings.ENV_TOKENS and settings.AUTH_TOKENS can now be removed.


Reference Implementation
========================

TBD


Change History
==============

