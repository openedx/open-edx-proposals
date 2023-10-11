0004 Release Data in catalog-info.yaml
######################################

Status
******

**Accepted**

Context
*******

* Currently we have 2 high-level metadata files that will live in every repo. The ``openedx.yaml`` file and the ``catalog-info.yaml`` file.

* The only information in ``openedx.yaml`` that is still useful and kept up-to-date is the ``release`` tag that tells you what repos are part of the twice-annual Open edX release.

Decision
********

We will use metadata in the ``catalog-info.yaml`` file to determine whether or not a repository is going to be tagged for Open edX releases.

Specification
*************

The data will be housed in a new annotation by the name of ``openedx.org/release``.  Non-null values of this key will indicate the branch that should be tagged for releases. A null value will indicate that the repository should not be tagged and a missing value will indicate that no decision has been made about the given repository.

.. code-block:: yaml
   :caption: Annotating that the ``master`` branch should be tagged for new releases.

   metadata:
     annotations:
       openedx.org/release: "master"

.. code-block:: yaml
   :caption: Annotating that the ``main`` branch should be tagged for new releases.

   metadata:
     annotations:
       openedx.org/release: "main"

.. code-block:: yaml
   :caption: Annotating that no branch should be tagged for new releases. This is the same as if the annotation was not added in the first place but more explicit.

   metadata:
     annotations:
       openedx.org/release: null

.. code-block:: yaml
   :caption: If the annotation is missing, we should consider that no decision has been made about tagging this repository.

   metadata:
     annotations:
       ...

Consequences
************

* :doc:`/archived/oep-0002-bp-repo-metadata` will be marked as superseded as it contains no more useful data.

* `Tooling related to the release <https://github.com/openedx/repo-tools/tree/master/edx_repo_tools/release>`_ will need to be updated.

* Useful release data in the current ``openedx.yaml`` files will need to be migrated to the relevant catalog-info.yaml files.

* Any examples of ``catalog-info.yaml`` will need to be updated to include an example of the new annotation.
