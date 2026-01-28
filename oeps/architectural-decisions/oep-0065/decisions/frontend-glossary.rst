.. _Frontend Glossary:

Frontend Glossary
#################

High Level Concepts
*******************

.. glossary::

  Guest
    A Guest is the :term:`Module` being loaded into a :term:`Site` via module federation.

  Host
    A Host is the :term:`Site` loading a :term:`Module` via module federation.

  Micro-frontend
    Micro-frontend is an industry standard term for small, composable, independently deployed pieces of a frontend. It has a specific and narrower meaning in Open edX's frontend. Open edX's decoupled frontend architecture has been called the "micro-frontend architecture" since 2018 or so, and the ``frontend-app-*`` repositories, specifically, are referred to as "micro-frontends" or "MFEs" for short. They're often called "micro-frontend applications" as well. Some might argue it's a misnomer, as many of our MFEs are quite large. Regardless, MFEs in Open edX refer to our independently deployed, siloed frontends which do not share dependencies, and which may contain one or more distinct parts of the overall frontend.

    To support a cohesive vernacular for our post-MFE architecture (the :term:`Module Architecture`), we propose co-opting "application" to refer to a sub-type of modules - :term:`application modules <Application Module>`. Each of the ``frontend-app-*`` repositories is really a collection of related applications co-located in the repository together because they share code and dependencies unique to their domain. For instance, the modules in the "learning" MFE - course outline, courseware, progress page, dates page, etc. - share a significant amount of code, but may be better thought of as a collection of related apps, not as "the learning app".

    The rest of this glossary reflects the proposed usage of "application".

  Micro-frontend Architecture
    This is the name for the Open edX frontend architecture based on ``frontend-platform``, ``frontend-build``, and independent ``frontend-app-*`` repositories that represent :term:`micro-frontends <Micro-frontend>`. It has been super-ceded by the :term:`Module Architecture`.

  Module
    A piece of code that provides some specific, tightly coupled functionality, and which is loaded into a :term:`Site` via one of several loading mechanisms (:term:`Imported Module`, :term:`Federated Module`, or :term:`Linked Module`) which can happen at buildtime or runtime.

    There are different sub-types of modules, such as :term:`Applications <Application Module>`, :term:`Plugins <Plugin Module>`, :term:`Services <Service Module>`, or :term:`Scripts <Script Module>`.

  Module Architecture
    This is the name for the Open edX frontend architecture based on ``frontend-base``, the :term:`shell`, :term:`module federation`, :term:`application modules <Application Module>`, and :term:`projects <Project>`.

  Module Config
    A Typescript file which represents the module-specific configuration of a :term:`Module` being deployed with module federation. They are found in :term:`Module Projects <Module Project>` or :term:`Implicit Projects <Implicit Project>`.

  Module Federation
    Module federation is a method that allows separate JavaScript builds to share code at runtime, forming a single application, while maintaining build and deployment independence. While not specific to Webpack, the initial `implementation of module federation <https://webpack.js.org/concepts/module-federation/>`_ is in Webpack.

  Module Library
    Module libraries are a collection of related modules co-located in a repository together (``frontend-app-*``) in the :term:`Module Architecture`. As an example, a library might be named ``frontend-app-learning``, and it would contain modules such as courseware, course outline, progress page, and dates page.  A module library may contain :term:`Modules <Module>` of all different subtypes.

    Note that this stands in contrast to how the ``frontend-app-*`` repositories were used in the :term:`Micro-frontend Architecture`.

  Open edX Frontend
    The complete set of interfaces shown to a user of an Open edX instance. Includes at least one independently deployed :term:`Site` and zero or more :term:`Federated Modules <Federated Module>`.

  Project
    A project is a repository representing a buildable and independently deployable portion of an operator's Open edX frontend. Often an operator will just have one project. It contains all the configuration and customization for a deployable part of the frontend. An operator may have multiple projects for a given Open edX frontend if they want to deploy it in multiple, independent pieces. This is common for larger organizations.

    There are two sub-types of Project: :term:`Site Projects <Site Project>` and :term:`Module Projects <Module Project>`.

  Remote
    A remote is module federation's term for an independently deployed bundle of :term:`Modules <Module>` loaded into a :term:`Site`. A remote may contain one or more potential :term:`guests <Guest>`. Module federation relies on the :term:`Host` knowing the location of the remote and its manifest, and uses that information to locate and load individual :term:`Modules <Module>` as :term:`guests <Guest>`.

  Remote Discovery
    Remote discovery is the process of locating :term:`Remotes <Remote>` on the Internet. This can happen at buildtime via the :term:`Site Config`, or it can happen at runtime. An important feature of webpack module federation is that it supports *runtime* remote discovery; other implementations do not. A :term:`Module` that wants to load its own :term:`Modules <Module>` via module federation, for instance, will cause the :term:`Shell` to engage in *runtime* remote discovery after loading the first :term:`Module`.

  Runtime Module Loading
    The capability to load :term:`modules <Module>` at runtime, rather than at buildtime. This allows a frontend system to remain decoupled with respect to its build and deployment, while creating a seamless and more performant experience for users.

  Shared Dependencies
    The capability to share an application's dependencies (such as React or Open edX's Paragon) between independently deployed modules. Sharing dependencies has a significant impact in overall download size for frontend clients.

  Shell
    The shell is the top-level components in a :term:`Site` which visually and logically wrap :term:`Modules <Module>`. The shell also provides shared dependencies used by :term:`Modules <Module>`.

  Site
    An independently deployed portion of the :term:`Open edX Frontend`. A Site renders the header, footer, and loads one or more :term:`Modules <Module>`. A frontend may consist of one Site, or multiple independent Sites, depending on an operator's needs. Navigating between Sites involves a full-page refresh and downloading a new set of dependencies.

  Site Config
    A Typescript file which represents the configuration of a :term:`Site`. It must adhere to the ``SiteConfig`` TypeScript interface. It is *runtime* code. They are found in :term:`Site Projects <Site Project>` or :term:`Implicit Projects <Implicit Project>`.

Project Sub-types
*****************

Related: See :term:`Project` in the `High Level Concepts`_ section.

.. glossary::

  Site Project
    A Site Project is a sub-type of :term:`Project` which represents a :term:`Site`. A Site Project consists of a :term:`Site Config` file, various development files (tsconfig.json, .eslintrc.js, jest.config.js, etc.), and the code and stylesheets that represent an operator's customizations to the :term:`Site`. This will primarily consist of custom :term:`Modules <Module>` to modify or extend the functionality of the :term:`Site`.

  Module Project
    A Module Project represents a group of :term:`Modules <Module>` to be deployed together via module federation. It requires a ``config`` field in ``package.json`` with a ``name`` for the deployment and an ``exposes`` field describing the modules to be federated. It optionally requires a ``module.config.tsx`` file to provide module-specific configuration. Finally, a Module Project must re-export any modules from its dependencies that it wishes provide via module federation.

  Implicit Project
    An implicit project is one that has been created in a ``frontend-app-*`` repository by adding a Site Config or Module Config file to the source code and running the appropriate ``openedx`` CLI command.

Module Sub-types
****************

Related: See :term:`Module` in the `High Level Concepts`_ section.

.. glossary::

  Application Module
    A sub-type of :term:`Module` that represents a logical portion of the UI of a :term:`Site`. A ``frontend-app-*`` repository will contain one or more application modules.

    Application Modules are loaded at a path within a :term:`Site`, such as ``/learning`` or ``/profile``. They are a specialized kind of :term:`Module` fulfilling a core function of the :term:`Open edX Frontend`. They are known, registered entities, and other :term:`Modules <Module>` can query the :term:`Shell` configuration to locate, navigate, or interact with them.

  Plugin Module
    A plugin is a :term:`Module` that adheres to the rules of the frontend plugin framework.  Plugins are optional and must be loaded into a :term:`Plugin Slot`.

  Service Module
    A service module is an implementation of one of the services in frontend-base, such as logging or analytics.

  Script Module
    A script module adds arbitrary functionality or third-party scripts to a :term:`Site` via script tags. They are generally, but not always, non-visual in nature.

Module Loading Methods
**********************

Related: See :term:`Module` in the `High Level Concepts`_ section.

.. glossary::

  Federated Module
    A Federated Module is loaded into a :term:`Site` at runtime via module federation. Federated modules are deployed independently from :term:`Sites <Site>`.

  Imported Module
    An Imported Module is an :term:`Application Module` that has been included in the :term:`Site Config` by, quite literally, being imported into it. These modules are bundled with the :term:`Site` at buildtime. It's generally desirable to lazy load them at runtime to keep initial bundle size small.

  Linked Module
    A linked module is an external website that a :term:`Site` can navigate to. This may be another :term:`Site` as defined in this glossary, or it may just be some other webpage, such as a support portal. Linked Apps are an important architectural option which allows us to do partial or gradual updates of our :term:`Open edX Frontend` by dividing it in pieces. If a frontend needs to migrate through breaking changes in its dependencies, like React or React Router, for instance, we can use this mechanism to split the :term:`Site` in two and migrate :term:`Application Modules <Application Module>` from one :term:`Site` to the other as they update the dependency with the breaking change. It will also allow some :term:`Application Modules <Application Module>` to stay as independently deployed, :term:`legacy MFEs <Micro-frontend>`, for instance. It consists of an identifier for an :term:`Application Module <Application Module>`, and a URL where it's located.

Plugins
*******

Related: See :term:`Plugin Module` in the `Module Sub-types`_ section.

.. glossary::

  Imported Plugin
    An imported plugin is loaded into a :term:`Site` at *buildtime* via the :term:`Site Config`. It does not include a security sandbox around the plugin, though it includes an error boundary.

  Federated Plugin
    A federated plugin is loaded into the :term:`Site` at *runtime* via module federation. It does not include a security sandbox around the plugin, though it includes an error boundary.

  IFrame Plugin
    An iframe plugin is loaded into the :term:`Site` at *runtime* via an iframe. It is the only way to safely load third-party or untrusted code, but should be used sparingly because it impacts performance significantly.

  Plugin Slot
    A designated place in the component hierarchy that will accept :term:`Plugins <Plugin Module>`. Plugin slots have documented layout expectations, configuration requirements, and contextual data that they share with :term:`Plugins <Plugin Module>`.

Change History
**************

* Document created
* `Pull request #626 <https://github.com/openedx/openedx-proposals/pull/626>`_
