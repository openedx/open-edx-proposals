=========================================
OEP-4: Application Authorization (Scopes)
=========================================

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-4 <oep-0004-arch-oauth-scopes>`
   * - Title
     - Application Authorization (Scopes)
   * - Last Modified
     - 2020-03-23
   * - Author
     - Eddie Fagin (eddie@edx.org)
   * - Arbiter
     - Dave Ormsbee (dave@edx.org)
   * - Status
     - Approved
   * - Type
     - Architecture
   * - Created
     - 2016-06-20

Abstract
========

Open edX application authorization guidelines.

The Open edX platform currently has the capability to act as an OAuth2 [#rfc6749]_ provider (which is distinctly different from its OAuth2 client capability i.e. the "login with Social Network X" feature).

Site admins can create long-lived OAuth2 client application keys that can in turn be exchanged for shorter-lived access and refresh tokens. These keys can optionally be scoped down to a custom list of available authorizations.

We did not have a cohesive strategy for creating or managing scopes across the Open edX platform and had reached a point where further maturity was required in this area.

This proposal puts forward a strategy that is is straightforward for both application developers (those who need to know about the available scopes) and Open edX platform admins and service teams (those who need to implement the restrictions).

The goals of the proposed strategy are to:

* restrict the behavior of external applications that need to integrate with an Open edX deployment, in line with the principle of "least privilege"

* enable more of those applications with low friction (optimize for ease of integration over ease of development)

* centralize legislation of authorization platform-wide, but federate and decentralize enforcement

* avoid dictating technology and protocol choices, but have clear guidance for implementation practices

See `Current Scopes and Filters`_ for documentation of existing Open edX scopes and filters.

Motivation
==========

The Open edX platform, in its current state, has a very limited mechanism for restricting the authorization of third-party applications to access user data. This limitation has dampened the ability of the platform to enable an ecosystem of third party applications.

Application developers have a harder time convincing their users that those users should grant these applications "access to everything the user can see and do", especially for special purpose applications that only need a small slice of the user's data to operate.

Instead of needing to trust both the platform and all applications built on the platform, our architecture should allow users to simply trust the platform's ability to delegate very specific data sets to third-parties at the user's request.

Rationale
=========

The discussion of scopes and application authorization has come up many times, but only recently have we (at edx.org) started to see a meaningful number of requests from external application developers that want to build on our platform.

For example, certain applications would like access to our edx.org course catalog for use cases like affiliate marketing and content repurposing. In this case, we want to be able to grant applications read-only access to our catalog, but not write access, and not access to any other resources (such as enrollments or user profiles).

Specification
=============

Data model
----------

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - Object
     - Description
   * - service user
     - an Open edX account created solely for server-to-server integrations; can have multiple associated applications
   * - application
     - an autonomous system that needs to integrate with an Open edX platform, owned by a single service user
   * - resource
     - named grouping of related capabilities. Will generally (not always) be a noun like "catalog"
   * - action
     - describes a high-level behavior and will generally be "read" or "write", but resources may use non-standard definitions where absolutely necessary (should be the exception)
   * - scope
     - a single authorization (naming convention: ``resource:action``) for an application relative to an Open edX instance; should only describe broad application authorization (as opposed to, say, fine-grained user authorizations)
   * - access token
     - a temporary credential that grants a set of scopes to an application

I sketched these relationships out in the diagram below. Note that instead of using "read/write" as actions, I noted the HTTP verbs instead, just to give a sense for the implementation details we've got in mind at this point. Same ideas though.

.. image:: oep-0004/authz_relationships.jpg
  :alt: A sketch of the proposed data model that connects service users to
        resources through applications, scopes, and resource actions.

Governance
----------

* Centralize the complete list of scopes but decentralize the associated metadata and definitions

* The central documented list of scopes must be readily available to application developers and service owners, and kept up to date

* Centralize application and token management but decentralize token enforcement

* Centralize common implementation patterns as we encounter them (logging, enforcement, etc)

* Access tokens may have one or more scopes associated to them by the central authorization service; tokens should be functionally opaque to applications

* Scopes should cover all RESTful web API endpoints [#REST]_ within a service, and may also cover other endpoints as desired by the service team

* Resources should not leak implementation details or internal names, i.e., instead of ``otto_cart``, consider just ``cart`` or ``shopping_cart``

* Resources should not, on the other hand, be too broad to be useful, i.e., instead of ``courseware``, consider ``discussions`` or ``catalog`` or whatever specific resource is of interest

Open questions
--------------

* How will we manage scope metadata, such as the human-readable "this is the permission you're authorizing" text for the OAuth pop-up (and localization considerations)?

* What is a good strategy for application lifecycle management, such as the recommended approval flow for key creation and revocation?

* What work is required to implement this proposal across the Open edX platform, including (but not limited to) the `edx-platform`_ codebase?

.. _`edx-platform`: https://github.com/edx/edx-platform

Backward Compatibility
=======================

There are no backwards compatibility issues with rolling out a centralized scopes list in our OAuth2 provider, or linking scopes to existing client applications.

However, there *might* be issues once we start enforcing scopes on individual service endpoints, as existing client applications that once might have had broader access may get suddenly cut off from data sources that we didn't know were being used. We will need to spend time up front understanding current access patterns in order to estimate the impact of this potentially breaking change and in order to correctly retrofit scopes onto existing application keys.

Current Scopes and Filters
==========================

This section documents the active scopes and filters used for Open edX endpoints.

For more details regarding how scopes are enforced in the LMS and other IDAs, see the `ADR on Enforcing Scopes in LMS APIs`_.

For more detail on how filters are used to add an additional layer of authorization, see this section of an `ADR on Organization and Users as Filters in OAuth Tokens`_ and this `ADR on More General Filter Support`_.

.. _ADR on Enforcing Scopes in LMS APIs: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0006-enforce-scopes-in-LMS-APIs.rst
.. _ADR on Organization and Users as Filters in OAuth Tokens: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0007-include-organizations-in-tokens.rst#2-organization-and-users-as-filters-in-oauth-tokens
.. _ADR on More General Filter Support: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0011-scope-filter-support.rst

Claim Authorization
-------------------

The following scopes are used to add additional claims to the user's JWT OAuth Token. See the `Use JWT as OAuth Tokens ADR`_ for details on this decision.

.. _Use JWT as OAuth Tokens ADR: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0003-use-jwt-as-oauth-tokens-remove-openid-connect.rst#jwt-token

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Scope
     - Claims added to JWT Payload
   * - email*
     - * *email*: user's email address
   * - profile*
     - * *name*: user's name in their edX profile
       * *family_name*: user's last name
       * *given_name*: user's first name
       * *administrator*: whether user is_staff
   * - user_id
     - * *user_id*: The user's LMS user id

**\*** Designates a default scope in the table above.

Endpoint Authorization
----------------------

The following scopes and filters are used to provide authorization to API endpoints as described in this OEP.

.. list-table::
   :header-rows: 1
   :widths: 20 60 20

   * - Scope
     - Description
     - Usage
   * - certificates:read
     - * Authorizes reading a user's course certificates.
     - * `CertificatesDetailView`_
   * - grades:read
     - * Authorizes reading grades for a user's enrolled courses.
     - * `CourseGradesView`_
   * - read*
     - * Provides no specific authorization.
     -
   * - tpa:read
     - * Authorizes reading of third-party auth data.
       * See the `Third-Party Auth Scope ADR`_ for more details.
     - * `UserMappingView`_
   * - write*
     - * Provides no specific authorization.
     -

**\*** Designates a default scope in the table above.

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Filter
     - Description
   * - content_org:<org_id>
     - * Limits permissions to the current course's organization.
       * See this `Organization and User Filters in OAuth Tokens ADR Section`_ for more details.
   * - tpa_provider:<provider_id>
     - * Limits permissions to the given provider id.
       * See the `Third-Party Auth Scope ADR`_ for more details.
   * - user:me
     - * For a token created on behalf of a user (not created via a Client Credentials grant type), this filter restricts the token specifically for the granting user.
       * See this `Organization and User Filters in OAuth Tokens ADR Section`_ for more details.

.. _CertificatesDetailView: https://github.com/edx/edx-platform/blob/02b900cf641e75332fad3a4ebf3f3c7c6aa156d5/lms/djangoapps/certificates/apis/v0/views.py#L32
.. _CourseGradesView: https://github.com/edx/edx-platform/blob/ad2192e7fe9c3298cb82e3f3cc35b0e3e0c25924/lms/djangoapps/grades/rest_api/v1/views.py#L40
.. _Third-Party Auth Scope ADR: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0012-scope-and-filter-for-third-party-auth.rst
.. _UserMappingView: https://github.com/edx/edx-platform/blob/acce8baca4809e45ae25b009b449a51df3428faf/common/djangoapps/third_party_auth/api/views.py#L267
.. _Organization and User Filters in OAuth Tokens ADR Section: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0007-include-organizations-in-tokens.rst#2-organization-and-users-as-filters-in-oauth-tokens

Rejected Alternatives
=====================

.. todo

N/A

Change History
==============

2020-03-23: Add `Current Scopes and Filters`_ section.

.. rubric:: Footnotes

.. [#rfc6749] For more information on OAuth2, please see the `OAuth2 specification <https://tools.ietf.org/html/rfc6749>`_.

.. [#REST] The Open edX REST API conventions currently live on the Wiki at `<https://openedx.atlassian.net/wiki/display/AC/edX+REST+API+Conventions>`_
