Open edX Course Roles Proposal Table
###################################################

edX Platform - student_courseaccessrole
---------------------------------------
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

edX Platform - course_roles_role
--------------------------------
.. list-table:: 
   :widths: 15 75

   * - **System Users**
     - 
       * Roles are assigned by instructors in the LMS and by CMS admins in the CMS. 
       * Roles are assigned to users (other than students) who need elevated access in the LMS or CMS, but roles may also apply to other systems.
       * Roles can be assigned in the django admin dashboard.
   * - **System Role Options**
     - 
       * Once implemented, the planned roles include current student_courseaccessrole roles and potentially comment_client roles.
       * Existing roles and role assignments would be migrated from student_courseaccess and potentially comment_client.
   * - **Example Use Cases**
     - 
       * Course level access
       * Course Level Access, granted org or instance wide
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
       * Roles assigned on a course by course basis, as org wide basis, or a system/instance wide basis.
   * - **Data Model**
     - 
       * All tables are in the edx-platform LMS database
          * course_roles_role table with name, id fields
          * course_roles_permission table with name, id fields
          * course_roles_rolepermission table with role_id, permission_id, id fields
          * course_roles_userrole table with user_id, role_id, course_id, organization_id, id fields
          * course_roles_service table with name, id fields
          * course_roles_roleservice table with role_id, service_id, id fields
       
       *Note: If the course_id column is empty the role is org wide, if the organization_id and course_id are empty the role is instance/system wide.*
    