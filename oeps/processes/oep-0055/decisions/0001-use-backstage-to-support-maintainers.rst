0001 Use Backstage to Support Maintainers
#########################################

Status
******

**Provisional**

Context
*******

* :doc:`OEP-55 <../../oep-0055-proc-project-maintainers>` laid out that we should document the maintainers of a component.

* As a maintainer it is hard to know what components you are maintaining.

* As a community member it is currently difficult to quickly look up who the
  maintainers are for multiple components quickly.

* Given the number of repos that make up the Open edX platform and the
  complexity of the job of maintaining them, we need a system for cataloging,
  and making transparent the critical details of our repositories.

Decision
********

To solve the above issues and to enable the potential to ease other
discoverability and maintainership burdens, the Open edX community shall
maintain a `backstage`_ instance and related metadata.

Consequences
************

* To support the backstage instance ownership data will have to be stored in a
  structured way.  We will follow Backstage best practices and store this data
  in a ``catalog-info.yaml`` file in each component repository.

* Once a backstage instance exists we have the opportunity to further leverage
  it as a centralized access point for maintainers as well as community members
  to quickly and easily assess different components and find relevant links to
  code, documentation, owners, etc.

* Backstage will also give us the ability to expose other health metrics to
  maintainers so they can more easily assess the components they own.

* As a result of adding the ``catalog-info.yaml`` file we end with two top-level
  files that have very similar data.  If this decision is not reversed after the
  initial pilot, it may make sense to revisit this consequence and see if we can
  consolidate this metadata into a single file. `tcril-engineering#331
  <https://github.com/openedx/tcril-engineering/issues/331>`_ has been created
  to follow up on this work.


Rejected Alternatives
*********************

Spreadsheet
===========

Pros
----

* Easy to build and get a high level view of the system.

Cons
----

* Not a beautiful UI for maintainers to work with.

* Data overload as we put more and more data into it with little to no control
  over the presentation of the data.

Owners in the Readme
====================

Pros
----

* Easy to see while in the repo.

Cons
----

* Harder to aggregate the data from the unstructured files in a way that would
  allow us to easily build a `single pane of glass`_ where maintainers can see
  all the things they own.

References
**********

* `Backstage`_ project site.

* `catalog-info.yaml`_ spec definition

* `Backstage system model`_ has detail about the different kinds of components
  and how they connect to each other.

* Sample ``catalog-info.yaml`` file.  Comments here starting with "Required"
  or "Optional" shouldn't appear in the file, they are here to help explain the
  sample:

  .. code:: yaml

    # This file records information about this repo. Its use is described in OEP-55:
    # https://open-edx-proposals.readthedocs.io/en/latest/processes/oep-0055-proc-project-maintainers.html

    apiVersion: backstage.io/v1alpha1
    # (Required) Acceptable Values: Component, Resource, System
    # A repo will almost certainly be a Component.
    kind: Component
    metadata:
      name: 'docs.openedx.org'
      description: "The root documentation site for https://docs.openedx.org"
      links:
        - url: "https://docs.openedx.org"
          title: "Deployed Site"
          # Backstage uses the MaterialUI Icon Set.
          # https://mui.com/material-ui/material-icons/
          # The value will be the name of the icon.
          icon: "Web"
      annotations:
        # (Optional) Annotation keys and values can be whatever you want.
        # We use it in Open edX repos to have a comma-separated list of GitHub user
        # names that might be interested in changes to the architecture of this
        # component.
        openedx.org/arch-interest-groups: "feanil"
    spec:

      # (Required) This can be a group (`group:<github_group_name>`) or a user (`user:<github_username>`).
      # Don't forget the "user:" or "group:" prefix. Groups must be GitHub team
      # names in the openedx GitHub organization: https://github.com/orgs/openedx/teams
      owner: group:docs.edx.org-maintainers

      # (Required) Acceptable Type Values: service, website, library
      type: 'website'

      # (Required) Acceptable Lifecycle Values: experimental, production, deprecated
      lifecycle: 'production'

      # (Optional) The value can be the name of any known component.
      subcomponentOf: '<name_of_a_component>'

      # (Optional) An array of different components or resources.
      dependsOn:
      - '<component_or_resource>'
      - '<another_component_or_resource>'

.. _Backstage: https://backstage.io
.. _Backstage system model: https://backstage.io/docs/features/software-catalog/system-model
.. _catalog-info.yaml: https://backstage.io/docs/features/software-catalog/descriptor-format
.. _single pane of glass: https://www.webopedia.com/definitions/single-pane-of-glass/
