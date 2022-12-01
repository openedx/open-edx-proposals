Deploying Open edX on Kubernetes Using Helm
###########################################


Status
******

Draft


Context
*******

For most of the history of the Open edX project, production deployments were done primarily using Virtual Machines that had been provisioned using the ansible scripts in the ``edx/configuration`` repository. Over time, enthusiasm for container-based deployments grew, eventually becoming the officially recommended way to deploy Open edX in production (see `OEP-45 Configuring and Operating Open edX`_ and related decisions).

A popular way to deploy containerized applications is to use `Kubernetes`_. However, Kubernetes has a lot of complexity so the overhead it brings is usually not worthwhile for small, singe-instance deployments. For that reason (as explained in OEP-45) as well as a general lack of experience with Kubernetes, to date there has been no officially recommended way to deploy Open edX on Kubernetes. Although Tutor (see `OEP-45/decisions/0001 Tutor as a replacement for edx/configuration`_) does include basic support for Kubernetes, it is focused only on a basic working deployment, and lacks the flexibility and capability that operators require for large, multi-instance deployments.

Despite this, many major commercial Open edX hosting providers have migrated toward deploying Open edX on Kubernetes. In doing so, they developed several different approaches for doing so, each fairly different from each other but all focused on deploying Tutor-generated images into a Kubernetes cluster. This has resulted in duplicated effort and made it difficult to share best practices and lessons learned with each other. For further context, read the forum discussion `Deploying multiple Open edX instances onto a Kubernetes Cluster with Tutor`_.


.. _Kubernetes: https://kubernetes.io/
.. _Deploying multiple Open edX instances onto a Kubernetes Cluster with Tutor: https://discuss.openedx.org/t/tech-talk-demo-deploying-multiple-open-edx-instances-onto-a-kubernetes-cluster-with-tutor/4641


Decision
********

For Open edX operators who wish to deploy Open edX on Kubernetes, community-maintained `Helm Charts`_ will be used to simplify the process of deploying one or more Open edX instances onto a Kubernetes cluster, while following relevant best practices.

Building on the decisions in `OEP-45 Configuring and Operating Open edX`_ and `OEP-45/decisions/0001 Tutor as a replacement for edx/configuration`_, the Helm charts will aim only to support Docker images built by Tutor.

As the needs and deployment scale of Open edX operators can vary significantly, the Helm charts will be designed to be flexible, incorporating sensible defaults but allowing customization of almost every aspect of the deployment. This includes supporting various cloud providers.

The goal is for these Helm charts to be developed and maintained collaboratively, by the Open edX Operators that are using them. All relevant best practices from both the Open edX and Helm projects should be followed from the start, such as `OEP-55 Project Maintainers`_.

.. _OEP-45 Configuring and Operating Open edX: https://open-edx-proposals.readthedocs.io/en/latest/architectural-decisions/oep-0045-arch-ops-and-config.html
.. _OEP-45/decisions/0001 Tutor as a replacement for edx/configuration: https://open-edx-proposals.readthedocs.io/en/latest/architectural-decisions/oep-0045/decisions/0001-tutor-as-replacement-for-edx-configuration.html
.. _OEP-55 Project Maintainers: https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0055-proc-project-maintainers.html
.. _Helm Charts: https://helm.sh/


Consequences
************

Helm Considerations
===================

Helm is the best known package manager for Kubernetes. It's status as a graduated project from the Cloud Native Computing Foundation is a clear indication of the thriving adoption, open governance and commitment to the open community.

Helm makes it possible to packages and publish charts, which can be rendered as kubernetes manifests. This charts provide robust support for versioning, deployment and rollback. At the same time Helm makes it easy for operators to customize application configurations during deployment and for developers to provide sensible defaults.

Charts are build with the Go language templating engine which is a drawback since it means adding a new language to the ecosystem with a new learning curve. This is considered acceptable due to the large adoption of Helm in the devops community.


Best Practices
==============

**Cloud Providers**

As much as possible, the Helm chart should aim to be agnostic toward the underlying cloud provider used (e.g. AWS, Azure, Google Cloud Platform, etc.). In cases where provider-specific code is required, the Charts should be written in such a way that they can be configured to work with multiple providers, and not e.g. only support AWS.

**Helm subcharts and modularity**

The project should aim to make the charts available in a way that is composable and granular. Extending a single subchart and using it in the composition should be a better and faster alternative than forking the whole project.


Industry Best Practices
=======================

The recommendations above are heavily inspired by the following resources:

* https://helm.sh/docs/chart_best_practices/


Alternatives Considered
***********************

Building more Kubernetes support into Tutor
===========================================

Enhancing the current support for tutor core was not considered since it would go against one of the goals of the project as `explained by the author`_.

.. _explained by the author: https://github.com/overhangio/tutor/pull/675#issuecomment-1140919654

Custom Templating tool
======================

A `custom templating tool`_ built as a tutor plugin and leveraging kustomize for extension was demonstrated and considered as a possible alternative. It was ultimately rejected as Helm would provide more flexibility in extension without the need of maintenance.

.. _custom templating tool: https://github.com/eduNEXT/drydock


Implementation Strategy
***********************

Once this ADR has reached Provisional status:

1. A new repository will be created on GitHub, e.g. at https://github.com/openedx/openedx-helm-charts
2. The authors of this ADR will create a roadmap using GitHub Issues by defining the initial development issues.
3. Invite interested parties to become core contributors on the repo, and nominate some of them.
4. Participating core contributors will then complete the roadmap and begin developing an MVP.


References
**********

A `working proof of concept`_ that was writen as part of the research for this ADR. See `2022-11-22 meeting recap`_ for further information.

.. _`working proof of concept`: https://github.com/open-craft/tutor-contrib-multi
.. _`2022-11-22 meeting recap`: https://discuss.openedx.org/t/deploying-open-edx-on-kubernetes-using-helm/8771


Change History
**************

2022-11-30
==========

* Document updated to become an ADR of OEP-45 instead of a standalone OEP.


2022-08-19
==========

* Document created
* `Pull request #372 <https://github.com/openedx/open-edx-proposals/pull/372>`_
