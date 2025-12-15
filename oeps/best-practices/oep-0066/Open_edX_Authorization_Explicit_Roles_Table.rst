Open edX Authorization Systems Explicit Roles Table
###################################################

edX Platform - Course Roles
---------------------------
.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Roles are assigned by instructors in the LMS and by CMS admins in the CMS. 
       * Roles are assigned to users (other than students) who need elevated access in the LMS or CMS, but roles may also apply to other systems.
       * Roles can be assigned in the django admin dashboard.
   * - **System Role Options**
     - 
       * Example roles as named in db: instructor, staff, beta_tester, etc.
       * Example roles as named in Instructor Dashboard/Course Teams: Admin, Staff, Beta Tester, etc.
   * - **Example Use Cases**
     - 
       * Course level access
       * Course Level Access, granted across an org of the system
       * Role/Permission set likely to be needed for many users
       * Roles need to be assignable within the UI
       * Roles need to be assignable by others with a specific role on the course
   * - **System Details**
     - 
       * LMS
          * Instructor role can access and assign course roles
          * Roles can be assigned in the membership tab on the instructor dashboard page
          * Roles assigned in the LMS may exist in the CMS
       * CMS
          * Admin role can access and assign course roles
          * Roles can be assigned on course team page
          * Roles assigned in the CMS must also exist in the LMS
       * Connected to the django admin dashboard through admin.py file.
       * Roles assigned on a course by course basis, an org by org basis, or for the entire system.
   * - **Data Model**
     - 
       * student_courseaccessrole database table in the edx-platform LMS database with course_id, id, org, role, user_id, _sdc_deleted_at fields 
       
       *Note: If the course_id column is an empty string the role is for the org, if the org and course_id are empty strings the role is for the entire system.*

comment_client - Discussion Roles
---------------------------------
.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Roles are assigned by instructors in the LMS.
       * Roles can be assigned to any user who needs elevated access to Discussions. 
       * The role of administrator cannot be removed from the instructor.
   * - **System Role Options**
     - 
       * Example roles as named in the db: Administrator, Moderator, etc.
       * Example roles as named in Instructor Dashboard: Discussion Admin, Discussion Moderator, etc.
   * - **Example Use Cases**
     - 
       * Discussion Service
   * - **System Details**
     - 
       * LMS
          * Instructor role can access and assign discussion roles
          * Roles can be assigned in the membership tab on the instructor dashboard page
          * Roles assigned in the LMS only apply to the LMS Discussions
          * Administrator role is given to the instructor by default and cannot be removed. Additional administrators can be added 
   * - **Data Model**
     - 
       * All tables are in the edx-platform LMS database
          * django_comment_client_role table with course_id, id, name fields
          * django_comment_client_role_users table with id, role_id, user_id, _sdc_deleted_at fields
          * django_comment_client_permission_roles table with id, role_id, permission_id fields 
          * django_comment_client_permission table with name field

edx Platform - Django Admin Flow
--------------------------------
.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Roles are assigned by superusers who have django admin access.
       * Any user can be assigned a permission set or a group (that has a permission set) through this process.
   * - **System Role Options**
     - 
       * `Currently Existing Roles <https://docs.google.com/document/d/1LZi5r3GYuSlrSRqfVKY1r61ysFQfaVLPJC0p-wVR-3g/edit#heading=h.xe2jv8xthu20>`_
       
       *Note: Student is not an explicit role at this time. It is an implicit role based on the existence of an enrollment.*
   * - **Example Use Cases**
     - 
       * Higher level system access
       * Specific or individualized access required
       * Higher level of control needed for assigning the roles
   * - **System Details**
     - 
       * Set in the Django Admin Control Panel
       * users for the instance with superuser role (highest access level) have access to this functionality
       * poor UX, poor discoverability, overly complex and possibly more error prone 

       *Note: Other services have their own Django Admin Panels that grant permissions.*
   * - **Data Model**
     - 
       * All tables are in the edx-platform LMS database
          * auth_permission table with codename, content_type_id, id_name fields
          * auth_group table with id, name fields
          * auth_group_permissions table with group_id, permission, permission_id, _sdc_deleted_at fields
          * auth_user table with id, email, username, and many other fields
          * auth_user_groups table with group_id, id, user_id, _sdc_deleted_at fields
          * auth_user_user_permissions table with id, permission_id, user_id fields 

edx-rbac
--------
.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Each service determines who can assign roles.
       * Each service determines which users roles can be assigned to.
   * - **System Role Options**
     - 
       * Each service that uses edX-rbac has its own list of explicit roles.
   * - **Example Use Cases**
     - 
       * Role needed only for a specific service
       * Roles needed across contexts
       * Ability to assign roles needed within the code
       * Ability to manage role permissions within the code
   * - **System Details**
     - 
       * Each service that follows the edX-rbac spec determines the scope of the role
       * Each service determines what other roles can explicitly grant access and where implicit access can come from (i.e. a system wide role may have implicit access)
       * Roles can be assigned for a feature or the service
       * The edX-rbac spec is designed to be usable by an instance, org, or service
       * Role assignments typically "stored" in the JWT cookie, but roles are stored in the db and therefore can be used from the db.
   * - **Data Model**
     - 
       * Each service that uses edX-rbac has its own db tables.

content_libraries - v2 Library Roles
------------------------------------

Legacy
^^^^^^^

.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Roles are assigned by the library creator in the CMS.
       * Roles can be assigned in the django admin dashboard.
   * - **System Role Options**
     - 
       * Roles:
          * admin = Administer users and author content
          * author = Author content
          * read = Read-only

   * - **Example Use Cases**
     - 
       * v2 Content Library Service
   * - **System Details**
     - 
       * Set in the CMS
       * Roles can be assigned through the CMS UI
       * Roles can be assigned through the LMS django admin dashboard
       * Roles are assigned per library.
       * Roles can be assigned in the UI by an admin for the library or a user with the global_staff role.
       * Connected to the django admin dashboard through admin.py file.
   * - **Data Model**
     - 
       * content_libraries_contentlibrarypermission table in the edx-platform LMS database with access_level, id, library_id, user_id, _sdc_deleted_at fields

New
^^^^^

.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Roles are assigned by the library creator or a library admin in the CMS.
   * - **System Role Options**
     - 
       * `Open edX Authorization Content Libraries Roles`_.
   * - **System Details**
     - 
       * Roles can be assigned through Studio.
       * Roles can be managed through the `openedx-authz <https://github.com/openedx/openedx-authz>`_ Rest API.
   * - **Data Model**
     - 
       * Uses `Casbin <https://casbin.org/>`_ for policy-based access control.
       * Policies stored in casbin_rule table with fields: id, ptype, v0, v1, v2, v3, v4, v5.
       * Policy structure: subject (role/user), action (role/permission), scope (pattern), effect.

.. _Open edX Authorization Content Libraries Roles: https://openedx-authz.readthedocs.io/en/latest/concepts/core_roles_and_permissions/content_library_roles.html#roles
