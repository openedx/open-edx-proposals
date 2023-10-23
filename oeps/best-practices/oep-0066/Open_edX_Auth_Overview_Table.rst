Open edX Auth Overview Table
----------------------------
.. list-table:: 
   :widths: 50 50
   :header-rows: 1

   * - Authentication (AuthN)
     - Authorization (AuthZ)
   * - Users should receive a JWT Token once authenticated.
     - Access is determined using a combination of the explicit and implicit role gratning. Logic may check a combination of the systems (or just one) and may check them in any order. Logic may use && or || depending on the specific Authn and Authz needs.
   * - Each Open edX instance may utilize different authentication options.
     - Roles assigned with edx-rbac are stored on the JWT and retrieved as part of `JWT creation <https://github.com/openedx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/jwt.py#LL31C8-L31C8>`_
   * - 
     - Scopes are stored on the JWT and retrieved as part of `JWT creation <https://github.com/openedx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/jwt.py#LL31C8-L31C8>`_. In this context, scopes limit access to the token bearer and control which optional claims are included in the token.
   * - 
     - Individual models have helper functions to determine access level (role) of the users. 

       `Example <https://github.com/openedx/edx-platform/blob/master/common/djangoapps/student/roles.py#L118>`_
