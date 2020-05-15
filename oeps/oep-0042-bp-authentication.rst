######################
OEP-42: Authentication
######################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-42 <oep-0042-arch-authentication>`
   * - Title
     - Authentication
   * - Last Modified
     - 2020-02-28
   * - Authors
     - Robert Raposa, Nimisha Asthagiri, Julia Eskew
   * - Arbiter
     - Jinder Singh
   * - Status
     - Under Review
   * - Type
     - Best Practice
   * - Created
     - 2020-02-28
   * - `Review Period`
     - 2020-04-30 - 2020-05-15

Summary
=======

Provides the current best practices for authentication in the Open edX platform, including :ref:`summarizing authentication decisions<Decisions>` made across the platform, and documenting authentication related `Standardized Libraries and Utilities`_.

Context
=======

Authentication in the Open edX platform has historically been complicated and inconsistent. A variety of efforts have been accomplished to attempt to simplify, consolidate, and standardize the methods of authentication used. To date, there has not yet existed a single reference for the variety of decisions made, as well as an index to documentation related to authentication.

Defined Terms
=============

Authentication (AuthN)
----------------------

Authentication is the verification of the identity of a user, which typically initiates at a “login” application point. Authentication is required whenever we need to identify a client/caller/user/etc.

Authorization (AuthZ)
---------------------

.. note::

  Authorization is generally out of scope of this OEP. This definition is here to clarify the difference from Authentication.

Authorization is the granting of permission of a certain user to perform specific operations in an application. A user can also delegate an application to be authorized to perform operations on their behalf without being logged in or authenticated, which is the basis of OAuth.

Identity Provider (idP)
-----------------------

From the `Wikipedia article on Identity provider`_:

  An Identity Provider, also known as Identity Assertion Provider, can:

  -  provide identifiers for users looking to interact with a system

  -  assert to such a system that such an identifier presented by a user is known to the provider

  -  possibly provide other information about the user that is known to the provider

.. _Wikipedia article on Identity provider: https://en.wikipedia.org/wiki/Identity_provider

Independently Deployable Application (IDA)
------------------------------------------

An Open edX specific term used to describe separate applications that make up a complete Open edX installation. Examples of IDAs are: LMS + Studio (edx-platform), Insights, ECommerce, Credentials, etc.

JSON Web Token (JWT)
--------------------

From `JWT.io Introduction`_:

  JSON Web Token (JWT) is an open standard (`RFC 7519 <https://tools.ietf.org/html/rfc7519>`__) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the HMAC algorithm) or a public/private key pair using RSA.

.. _JWT.io Introduction: https://jwt.io/introduction/


OAuth 2.0 (OAuth2)
------------------

An authorization framework that enables applications to obtain limited access to user accounts on an HTTP service. See a `simplified oauth2 explanation by Aaron Parecki`_ or dig into the full `OAuth2 specification`_.

.. _simplified oauth2 explanation by Aaron Parecki: https://aaronparecki.com/oauth-2-simplified/
.. _OAuth2 specification: https://oauth.net/2/

OpenID Connect (OIDC)
---------------------

.. note::

  We are no longer using OpenID Connect. This definition is only to provide context for historical decisions to move away from OpenID Connect.

From `OpenID Connect Discovery 1.0`_ document:

  OpenID Connect 1.0 is a simple identity layer on top of the OAuth 2.0 [RFC6749] protocol. It enables Clients to verify the identity of the End-User based on the authentication performed by an Authorization Server, as well as to obtain basic profile information about the End-User in an interoperable and REST-like manner.


.. _OpenID Connect Discovery 1.0: https://openid.net/specs/openid-connect-discovery-1_0.html

Decisions
=========

There are a number of authentication related decisions that have been made. This OEP is meant to be updated over time as we gain more information, including links to other related `Architectural Decision Records (ADRs)`_.

Single Identity Provider and OAuth Authorization Server
-------------------------------------------------------

The LMS will act as the sole identity provider and OAuth authorization server for all other surrounding IDAs. The LMS can provide information about the identity of the user to the other IDAs. The LMS also provides Single Sign-On (SSO) and Single Logout (SLO) to automatically log in and out IDA users.

The implementation can primarily be found in:

* `oauth_dispatch (edx-platform)`_: Identity provider implementation for `OAuth2 and JWTs`_ below.

OAuth2 and JWTs
---------------

The currently supported and recommended method of authentication is an OAuth 2.0 implementation using `JSON Web Tokens (JWTs)`_ as OAuth tokens.

Here are a variety of details around this decision:

* Standardize on the `Django OAuth Toolkit (DOT) library`_ to support our OAuth2 implementation.

  * Read the `ADR on Django OAuth Toolkit (DOT)`_.

* JWT Authentication is the standard method of authentication in Open edX.

  * Use JWT and DOT to implement OAuth2. See the `ADR to use JWTs as OAuth Tokens`_ for more details on this decision.

  * JWTs are now the only fully supported type of access token.

  * All JWTs will be signed using "asymmetric" cryptographic keys, as detailed in this `ADR on Asymmetric JWTs`_.

  * For authentication from our micro-frontends, a shared cookie is used to store the JWT and usable across IDAs, as detailed in this `JWT Cookies ADR`_.

Implementation of all the OAuth2/JWT APIs supported by DOT in the LMS Identity Provider can be found in `oauth_dispatch (edx-platform)`_.

* As of the Juniper Open edX release, the deprecated implementation of OAuth2 using OpenID Connect (OIDC) and the `Django OAuth2 Provider (DOP) library`_ has been fully removed. See this `oauth_dispatch as router ADR`_ for additional details of how this transition was implemented in edx-platform, and to better understand the history of ``oauth_dispatch``.

.. _JSON Web Tokens (JWTs): https://tools.ietf.org/html/rfc7519
.. _Django OAuth Toolkit (DOT) library: https://django-oauth-toolkit.readthedocs.io/en/latest/
.. _ADR on Django OAuth Toolkit (DOT): https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0002-migrate-to-dot.rst
.. _ADR to use JWTs as OAuth Tokens: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0003-use-jwt-as-oauth-tokens-remove-openid-connect.rst
.. _ADR on Asymmetric JWTs: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0008-use-asymmetric-jwts.rst
.. _JWT Cookies ADR: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0009-jwt-in-session-cookie.rst
.. _oauth_dispatch (edx-platform): https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/README.rst
.. _Django OAuth2 Provider (DOP) library: https://django-oauth2-provider.readthedocs.io/en/latest/
.. _oauth_dispatch as router ADR: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0004-oauth-dispatch-as-router.rst#L33


OAuth2 and Bearer Tokens
------------------------

This section refers to Bearer Tokens as documented in `OAuth 2.0 RFC: Bearer Token Usage`_.

In this section, Bearer Tokens refers to tokens sent with "Bearer" in the Authorization request header. In contrast, our JWTs are sent using "JWT" in the Authorization request header. Note that the `Authorization request header`_ contains the credentials used to *authenticate* a user, even though it uses the term *authorization*.


Currently, only the mobile applications (iOS and Android) continue to use Bearer Tokens. In this method, the LMS issues a token to the mobile apps and the mobile apps include this token in their header when hitting IDA endpoints. The IDAs verify the token and either service or reject the request, based on the token validity.

All other usage of Bearer Tokens in Open edX has been deprecated. Mobile applications may one day move to JWTs as well, but that is a larger effort.

.. _`OAuth 2.0 RFC: Bearer Token Usage`: https://tools.ietf.org/html/rfc6750
.. _Authorization request header: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization

OAuth2 Token Security
---------------------

The communications between the browser, LMS, and IDA must all use `Transport Layer Security (TLS)`_ in order to keep the OAuth2 token secure, as anyone with the token can make a restricted IDA request.

This applies to all OAuth2 tokens, including those discussed in `OAuth2 and JWTs`_ and `OAuth2 and Bearer Tokens`_.

.. _Transport Layer Security (TLS): https://en.wikipedia.org/wiki/Transport_Layer_Security

Social (and Other) Authentication
---------------------------------

Open edX platform also supports several social authentication methods, such as Google, Facebook, and LinkedIn, along with other campus/business-specific authentication methods, including `SAML`_. These external authentication methods are used to integrate or link your edX identity to another network identity. However, once the identity link is established and an Open edX account is created, the LMS still functions as usual as the idP for all satellite IDAs, and uses Open edX (non-social) authentication methods described above.

The code for supporting third party authentication (SAML, Google, Facebook, etc), where the initiating identity provider is _not_ the Open edX LMS, is located in `third_party_auth (edx-platform)`_. This implementation is supported by the `python-social-auth library`_.

.. _SAML: https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language
.. _third_party_auth (edx-platform): https://github.com/edx/edx-platform/tree/master/common/djangoapps/third_party_auth
.. _python-social-auth library: https://github.com/omab/python-social-auth

Standardized Libraries and Utilities
------------------------------------

This section details a variety of authentication related libraries and utilities that Open edX has standardized on. It is important to keep to these standards in order to help keep Open edX more secure.

For any of the following solutions, it is important to avoid creating local alternatives inside an IDA. If a local alternative exists, it should either be deprecated and replaced by these standards, or requires an `Architectural Decision Record (ADR)`_ explaining why the exception is necessary and how the security of Open edX will continue to be ensured.

API Providers: Authentication Classes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`Django REST Framework (DRF)`_ is the standard library used by Open edX to implement REST APIs in Python. Learn more about `Authentication with Django REST Framework (DRF)`_ here.

The following are all DRF Authentication classes.

.. list-table::
   :widths: 60 40
   :header-rows: 1

   * - Authentication Class
     - Status
   * - `JwtAuthentication (edx-drf-extensions)`_
     - Supported
   * - `SessionAuthentication (django-rest-framework)`_
     - Supported
   * - `BearerAuthentication (edx-drf-extensions)`_
     - Deprecated, except mobile
   * - `BasicAuthentication (django-rest-framework)`_
     - * Exceptions Only
       * Requires an `Architectural Decision Record (ADR)`_ explaining why it is required.

Note: Our JwtAuthentication class is a subclass of JSONWebTokenAuthentication, which can be found in `drf-jwt`_, an open source fork of django-rest-framework-jwt that supports Django 2.2.

.. _Django REST Framework (DRF): https://www.django-rest-framework.org/
.. _Authentication with Django REST Framework (DRF): https://www.django-rest-framework.org/api-guide/authentication/#authentication
.. _JwtAuthentication (edx-drf-extensions): https://github.com/edx/edx-drf-extensions/blob/4d0f4de80681e5826cfbe3041ea4cda6cff87640/edx_rest_framework_extensions/auth/jwt/authentication.py#L25
.. _SessionAuthentication (django-rest-framework): https://www.django-rest-framework.org/api-guide/authentication/#sessionauthentication
.. _BasicAuthentication (django-rest-framework): https://www.django-rest-framework.org/api-guide/authentication/#basicauthentication
.. _BearerAuthentication (edx-drf-extensions): https://github.com/edx/edx-drf-extensions/blob/4d0f4de80681e5826cfbe3041ea4cda6cff87640/edx_rest_framework_extensions/auth/bearer/authentication.py#L18
.. _drf-jwt: https://pypi.org/project/drf-jwt/

Authenticated API Clients
~~~~~~~~~~~~~~~~~~~~~~~~~

The following are supported API clients that handle authentication using the supported methods documented in this OEP.

* `OAuthAPIClient (edx-rest-api-client)`_: A Python client for making authenticated server-to-server calls.
* `@edx/frontend-platform/auth (frontend-platform)`_: A JavaScript client for making authenticated calls from a micro-frontend.

.. _OAuthAPIClient (edx-rest-api-client): https://github.com/edx/edx-rest-api-client/blob/518e7291f2e90d6b9dce0f943749d59fa5c1fa42/edx_rest_api_client/client.py#L181
.. _@edx/frontend-platform/auth (frontend-platform): https://github.com/edx/frontend-platform/blob/master/README.md

OAuth Backend
~~~~~~~~~~~~~

Open edX uses `EdXOAuth2 (auth-backends)`_ to provide SSO across IDAs using OAuth2. For more general information, see `Specifying authentication backends in Django`_. This backend implementation uses the `python-social-auth library`_.

.. _EdXOAuth2 (auth-backends): https://github.com/edx/auth-backends/blob/1444a5fa650e01b6e24be77917259bca1d8eb1ea/auth_backends/backends.py#L35
.. _Specifying authentication backends in Django: https://docs.djangoproject.com/en/2.2/topics/auth/customizing/#specifying-authentication-backends
.. _python-social-auth library: https://github.com/omab/python-social-auth

Consequences
============

Although some of the work required to make these decisions a reality have been completed, there is still a variety of outstanding work and clean-up to be done.

* Since the LMS is the single authentication server, we need to remove the non-standard ``JWT_ISSUERS``.

  * Read this `ADR section on removing JWT_ISSUERs`_. The Ecommerce Service provides the largest obstacle to this effort.

* Not all JWTs are yet signed with "asymmetric" keys.

* Deprecation and removal of authentication libraries and utilities that are not part of our `Standardized Libraries and Utilities`_. Because removal can be costly and may not always get prioritized, start with appropriately marking functions and classes as deprecated to help minimize the contagion factor.

.. _ADR section on removing JWT_ISSUERs: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/decisions/0008-use-asymmetric-jwts.rst#remove-jwt_issuers

References
==========

* `Architectural Decision Records (ADRs)`_

.. _Architectural Decision Records (ADRs): https://open-edx-proposals.readthedocs.io/en/latest/oep-0019-bp-developer-documentation.html#adrs
