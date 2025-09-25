OEP-68: Multi-Tenant Support in Open edX Mobile Apps
####################################################

 .. list-table::
   :widths: 25 75

   * - OEP
     - :ref:`OEP-68 Multi-Tenant Mobile Support`
   * - Title
     - Multi-Tenant Support in Open edX Mobile Apps
   * - Last Modified
     - 2025-03-18
   * - Authors
     - 
       * Rawan Matar <rawan@zeitlabs.com>
       * Ivan Stepanok <ivan.stepanok@raccoongang.com>

   * - Arbiter
     - TBD
   * - Status
     - Draft
   * - Type
     - Architecture Decision
   * - Created
     - 2025-03-18
   * - Review Period
     - TBD
   * - References
     -
       * `Multi-tenant PR <https://github.com/zeit-labs/openedx-app-ios-contrib/pull/10>`_

.. contents::
  :local:
  :depth: 1

Abstract
********
This OEP proposes adding **multi-tenant support** to the Open edX mobile applications. 
Currently, the apps support only a single institution per build. 
This proposal introduces a mechanism for users to select their institution (tenant) at runtime, 
with each tenant providing its own branding, configuration, and API endpoints. 
This reduces duplication and enables a white-labeling approach 
within a single mobile app module.

Motivation
**********
The Open edX mobile app is increasingly used by multiple institutions. 
Releasing separate app builds for each tenant creates duplication, higher maintenance burden, 
and inconsistent user experience. Multi-tenancy enables:

* A single app build for multiple institutions.
* Dynamic branding (logo, color scheme, names).
* Institution-specific API and authentication endpoints.
* Future extensibility to fetch tenant definitions dynamically.

Current State
=============
- Mobile apps support only one tenant per build (YAML-based config).
- Persistence layer uses a single Core Data/SQLite DB, risking data leakage across tenants.
- Push notification manager doesn’t account for tenant context.
- Branding and login flows are static per build.

Decision
********
We propose supporting **multi-tenancy** in one app build by:

* Providing a tenant selector screen at login.
* Defining tenant metadata (branding, API URLs, auth URLs, etc.) in a JSON configuration file.
* Allowing runtime tenant switching without reinstallation.
* Maintaining isolated databases per tenant.
* Enhancing push notifications with tenant context.

Specification
*************

Tenant Selection
================
- On launch, show a tenant selector if multiple tenants are configured.  
- If only one tenant exists, preserve the current flow.  

Tenant Configuration
====================
Tenant metadata will be stored in a JSON config file, including:

* ``name`` (tenant_id)
* ``display_name``
* ``logo_url``
* ``primary_color``
* ``api_base_url``
* ``auth_url``

Example::

  {
    "tenants": [
      {
        "name": "tenant_a",
        "display_name": "University A",
        "logo_url": "https://example.com/logo.png",
        "primary_color": "#FF5733",
        "api_base_url": "https://tenant_a.example.com/api",
        "auth_url": "https://tenant_a.example.com/auth"
      }
    ]
  }

Database Management
===================
- Each tenant has its own Core Data/SQLite store.  
- Database name pattern: ``opendx_{tenant_id}_db``.  
- A `DatabaseManagerProvider` maintains tenant-specific DB managers.  
- Switching tenants reloads the persistence stack.  

Push Notifications
==================
- One device token is maintained.  
- Token registered with all tenants.  
- Payloads include ``tenant_id``.  
- Notifications routed to the correct tenant listener.  


Tradeoffs
=========
We considered three approaches:

**1. Single App + Bundled Config (proposed)**

- **Pros**:
  - Single codebase and release process, reducing duplication.  
  - Works offline immediately since tenant definitions are bundled.  
  - Reliable theming and branding without network dependency.  
- **Cons**:
  - Updating tenant metadata requires an app update (until remote fetch is added).  
  - Larger app size if many assets are included.  
- **Implications**:
  - **Push notifications**: One device token registered with all tenants, routed by ``tenant_id``.  
  - **Theming**: Strong, since assets/colors are local.  
  - **Offline**: Strong support — tenants always available.  

**2. Remote Configuration**  

- **Pros**:
  - Tenant metadata can change without an app update.  
  - Centralized management of tenant definitions.  
- **Cons**:
  - Requires robust caching and fallbacks to work offline.  
  - Higher runtime complexity and security considerations.  
- **Implications**:
  - **Push notifications**: Requires re-registration if tenant config changes.  
  - **Theming**: More flexible, but must be cached to avoid broken UI offline.  
  - **Offline**: Dependent on last successful fetch.  

**3. Separate Builds (current state)**  

- **Pros**:
  - Full isolation per tenant (bundle IDs, push notifications, store presence).  
  - Simple runtime logic.  
- **Cons**:
  - High maintenance and CI/CD overhead.  
  - Risk of divergence across builds.  
- **Implications**:
  - **Push notifications**: Simplest — separate certs per build.  
  - **Theming**: Static and guaranteed.  
  - **Offline**: Guaranteed, but at the cost of scalability.  

Impact on Single-Tenant Deployments
===================================
For single-tenant deployments, this proposal introduces minimal change:

- If only one tenant is defined in the configuration, the tenant selector is skipped and the app behaves exactly as today.  
- Branding, theming, and login flows remain static to that single tenant.  
- No additional runtime overhead is introduced for single-tenant cases.  

Alternatives
************
- Separate app per tenant (high maintenance).  
- Remote configuration (scalable but adds backend dependency).  
- Custom login screen only (limited branding flexibility).  

Impact
******
- **Users**: Access multiple institutions in one app.  
- **Institutions**: Branded experiences without custom builds.  
- **Developers**: Reduced duplication and simpler long-term maintenance.  
- **Backend**: No changes required, except for including ``tenant_id`` in notification payloads.  

Future Work
***********
- Fetch tenant metadata dynamically from an API.  
- Allow tenant switching post-login via settings/profile.  
- Adopt design tokens for consistent theming across platforms.  

Change History
**************
2025-03-18
==========
* Initial draft created based on ADR.  

2025-09-23
==========
* Added Tradeoffs and Single-Tenant Impact sections.  