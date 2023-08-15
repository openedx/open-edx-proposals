==============================================
OEP-XXXX: OEP RBAC (Role Based Access Control)
==============================================

.. This OEP template is based on Nygard's Architecture Decision Records.

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-XXXX <oep-XXXX-arch-role-based-access-control>`
   * - Title
     - RBAC (Role Based Access Control)
   * - Last Modified
     - 2023-08-15
   * - Authors
     - Hilary Sinkoff <hsinkoff@2u.com>
   * - Arbiter
     - Feanil Patel <feanil@axim.org>
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2023-08-15
   * - Review Period
     - TBD

*******
Context
*******

There are currently multiple ways to assign roles to users at multiple context levels (course, organization, instance). 
These allow for assigning a role to a user, but do not indicate what the usages of the role are or
allow for any modification of the roles themselves. 

There are currently two distinct systems for adding course level roles that are comingled in the UI.
The database tables involved in the first system (course access roles) is the student_courseaccessrole table 
and the database tables involved in the second system (discussion roles) are the django_comment_client_role table and associated tables.
Additionally, it is possible to grant a user access to a course using the Django Admin auth_permission table and associated tables.

The current systems require developer work across multiple repos, MFEs, and IDAs in order 
to add a new role, modify an existing role, or remove an existing role.

Additional flexibility from the system has been requested in order to add new roles, 
modify roles, create custom roles, understand role impact, and bulk add and remove role assignments.
The current systems do not allow for customization and user controlled modification of existing roles.


Decision
********

We will create a new system that utilizes permissions assigned to roles
which are assigned to users to grant access to different portions of the system.
This system will replace the two existing systems, course access roles and discussion roles. 
The new system will initially be rolled out for 1 - 3 new roles, but once it is proven 
effective, it will be rolled out for the roles that exist in course access roles and discussion roles. 
At that time we will sunset the usage of course access roles and discussion roles.

Consequences
************

Once completed this will remove one level of complexity from the overall authorization picture 
by lower the systems involved by one, however during the interim it will add complexity by increasing 
the number of involved systems and requiring all access checks to check for an additional value.

After implementation, access checks will check for a permission instead of a role. This will mean that 
if a new permission is added, each default role will need to be updated to indicate if it grants the new 
permission. It will also mean that if a new role is added, there will be no need to update each of the services 
that grant access as they will continue to check for the same permissions no matter how many roles grant those permissions.

Adding this new system will allow for eventual customized roles on an organization basis. 
This system will continue to allow for assigning a course level role to a user at 
an organization or instance level. 

References
**********

.. image:: oep-xxxx/rbac_overview.png

Change History
**************

2023-08-15
==========

* Document created
* `Pull request #XXX <https://github.com/openedx/open-edx-proposals/pull/XXX>`_
