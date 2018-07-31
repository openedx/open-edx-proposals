===================================
OEP-0027: Course Metadata Authoring
===================================

+-----------------+----------------------------------------------------------------+
| OEP             | :doc:`OEP-0027 </oeps/oep-0027-arch-course-metadata-authoring>`|
+-----------------+----------------------------------------------------------------+
| Title           | Course Metadata Authoring                                      |
+-----------------+----------------------------------------------------------------+
| Last Modified   | 2018-07-13                                                     |
+-----------------+----------------------------------------------------------------+
| Authors         | Farhanah Sheets, Michael Roytman, Nimisha Asthagiri            |
+-----------------+----------------------------------------------------------------+
| Arbiter         | Wes Mason                                                      |
+-----------------+----------------------------------------------------------------+
| Status          | Under Review                                                   |
+-----------------+----------------------------------------------------------------+
| Type            | Architecture                                                   |
+-----------------+----------------------------------------------------------------+
| Created         | 2018-07-13                                                     |
+-----------------+----------------------------------------------------------------+
| `Review Period` | 2018-08-01 - 2018-08-15                                        |
+-----------------+----------------------------------------------------------------+

Context
-------

An understanding of `edX subdomains`_ is a prerequisite for this OEP.

In the Open edX system, course content authoring occurs in the Studio tool, which focuses mainly on authoring of
course structure and content within a **Course Run**. A Course Run is discoverable on the edX marketing site via
the context of a **Catalog Course**. The **Catalog Course** data authoring needs a good user experience.

Today, Catalog Course metadata authoring occurs in multiple places, including the Studio, Ecommerce, and
Marketing sites. We introduced a new tool, called Publisher, to ease the Catalog Course metadata 
authoring process. The Publisher tool includes an inflexible workflow engine for the process of provisioning
a course. Currently, this course publish cycle can take up to fifteen days in order to provision 
a new course. 

We also run into data synchronization issues between the separate services since there are multiple sources of truth and
multiple writers of the relevant metadata.

This project will leverage edX's `decoupled frontend architecture`_ plan. In particular, we will be using phase 3 of the plan to separate
the Content Metadata Authoring frontend from the associated backend.

  .. _decoupled frontend architecture: https://openedx.atlassian.net/wiki/spaces/FEDX/pages/790692200/Decoupled+Frontend+Architecture
  .. _edX subdomains: https://openedx.atlassian.net/wiki/spaces/AC/pages/663224968/edX+DDD+Bounded+Contexts#edXDDDBoundedContexts-edXSubdomainMap


Decisions
---------

* We will no longer embed workflow into the core system. Rather, we will develop or enhance existing APIs to offload workflow and business process to systems and services that allow for more agile changes.

* Conceptually, we will separate the notion of Course within the edX system in the following ways:

  * **Course Learning Content**, a course-team created package of learning material, with its weekly and sub-weekly structure
    (i.e., Sections, Subsections, Units, etc.) and learning blocks (i.e., leaf xBlocks), tied to specific dates of offering.
     
    * This is what we call **(Studio) Course Run**, for the time being.
    * It is bound to the organization that authors the content.
    * Note: In the future, this concept may be further separated when we support content reuse across "Course Runs".
      At that time, we may distinguish between:

      * **Reusable Learning Content** (not tied down to specific dates nor structure) and
      * **Packaged Learning Content** (an "instantiation" of a "Reusable Learning Content" with structure, dates, and 
        configuration).

  * **Discoverable Course**, a learner-facing representation of a course that entices enrollment with marketing information
    such as the course's price and credit/degree outcomes.

    * This is what we call **Catalog Course**, for the time being.
    * It is bound to a specific marketing site.

  * **Learner Consumable Course**, a learner-engaging course, with read-optimized representation of the learning content. 

    * This is what we call **(LMS) Course Run**.
    * It is bound to a specific learner.

* Given the above distinctions, each `edX subdomain`_ will manage its own concept. Hence, the authoring logic
  implementation for each concept will also remain separate. This prevents us from needing to maintain these concepts across
  multiple domains: keep the authoring of Discoverable-Course (**Catalog Course**) within Discovery; keep the authoring of 
  Learning-Content-Course (**Course Run**) within Content Authoring. For example, concepts of price, marketing-publish-date,
  marketing sales, discounts, etc., do not need to bleed into the Content Authoring service.

* The following is Course Run metadata and its editing should be owned by the `Content Authoring subdomain`_:

  * course run dates

    * start
    * end
    * enrollment start
    * enrollment end
    * certificate availability

  * pacing type

* The following is Catalog Course metadata and its editing should be owned by the `Discovery subdomain`_:

  * course about image
  * instructors
  * seat price
  * seat type
  * other catalog course marketing data

  **Note:** course about image and instructors are currently managed as Course Run metadata but will transition to be Catalog Course metadata. 

* The Discovery service will be the single source of truth for the published version of the above data. Though to address the data
  synchronization issues, we will limit the editing of a piece of data to its single owning service. Anywhere else that a piece of data is editable
  will be made read-only.

* We will introduce a new front end for editing Catalog Course metadata, written using the latest recommended front end technologies and leveraging phase 3
of the `decoupled frontend architecture`_ plan.

* From the course author's perspective, we will provide a unified user experience by having the current Studio tool
  be the portal to authoring both Course Run metadata and Catalog Course metadata. So even though authoring of Catalog Course
  metadata will live in the Discovery subdomain, there will be a seamless transition between the front ends of Course Run
  Authoring and Catalog Course Authoring.

  * This will depend on upcoming architectural infrastructure work on single sign on across the edX microservices and a universal
    front end header and footer.

  .. _decoupled frontend architecture: https://openedx.atlassian.net/wiki/spaces/FEDX/pages/790692200/Decoupled+Frontend+Architecture
  .. _Discovery subdomain: https://openedx.atlassian.net/wiki/spaces/AC/pages/663224968/edX+DDD+Bounded+Contexts#edXDDDBoundedContexts-edXSubdomainMap
  .. _Content Authoring subdomain: https://openedx.atlassian.net/wiki/spaces/AC/pages/663224968/edX+DDD+Bounded+Contexts#edXDDDBoundedContexts-edXSubdomainMap
  .. _edX subdomain: https://openedx.atlassian.net/wiki/spaces/AC/pages/663224968/edX+DDD+Bounded+Contexts#edXDDDBoundedContexts-edXSubdomainMap

.. image:: oep-0027/publisher_v3_future.png
  :alt: A flowchart of the movement of Course Run metadata and Catalog Course metadata through edX systems in the future.

Consequences
------------

* By separating the concepts of Course Run metadata authoring from Catalog Course metadata authoring, we allow the overall
  edX system to integrate better with other external systems. We have found that other systems use Open edX for the Course
  Run learning experience and often choose to integrate with their own organizational Catalog Course management system.

  As a consequence, the authoring experience is bifurcated into two different subdomains in our system, Studio and Discovery. However,
  we do hope that the integration at the UI layer will avoid any disparate user experience. The separation of concerns in the back end
  allows us to have clearer integration points. We believe this is a worthwhile trade-off.

* By removing the workflow engine, we are moving to a model that enables self-service publishing for most courses and allows internal teams to 
  reduce the number of blocking approvals and to move to a more scalable system of monitoring and alerting rather than manual review. There are 
  risks associated with removing existing workflow and gates, but by offloading workflow to a specialized external system, there is a reduced 
  reliance on engineering to make changes around workflow and an ability to quickly make changes to workflow to respond to problems.

* In the past, course authors were able to edit data using multiple tools, primarily in the tool where the data was presented to learners. Now, 
  the authoring experience will be isolated to one location, potentially not where that data is surfaced to learners.

References
----------
