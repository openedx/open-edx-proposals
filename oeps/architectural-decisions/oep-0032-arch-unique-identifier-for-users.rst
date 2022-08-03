.. _oep-32:

OEP-32: Unique Identifier for Users
###################################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-32 <oep-0032-arch-unique-identifier-for-users>`
   * - Title
     - Unique Identifier for Users
   * - Last Modified
     - 2021-04-16
   * - Authors
     - Brian Wilson, Robert Raposa, Nimisha Asthagiri
   * - Arbiter
     - Olga Stroilova
   * - Status
     - Approved
   * - Type
     - Architecture
   * - Created
     - 2019-01-25
   * - `Review Period`
     - 2019-04-15 - 2019-04-29

Decision
********

We will use the *LMS user_id* to uniquely identify a user in the Open edX system. The *LMS user_id* is the ``id`` value of the user's row in the `Django auth_user`_ table in the Open edX LMS.

The *LMS user_id* should be used for all internal events. Additionally, the *LMS user_id* can be used for public events, REST APIs, and JavaScript APIs.

.. warning::

    A new external user id should still be used with third-parties under certain circumstances. This is especially important when the user id is sent along with other :doc:`Personally Identifiable Information (PII) <oep-0030-arch-pii-markup-and-auditing>`. See the `ADR for the edx-platform external_user_ids Django app`_ for more details.

.. _Django auth_user: https://docs.djangoproject.com/en/2.0/topics/auth/default/#user-objects
.. _ADR for the edx-platform external_user_ids Django app: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/external_user_ids/docs/decisions/0001-externalid.rst

Context
*******

In 2016, the analytics team decided to use the the *LMS user_id* as a consistent user identifier for analytics events, and this Open edX Proposal (OEP) further documents and builds upon that decision.

In addition to needing a consistent user identifier for internal events, we also need one for public events, REST APIs and JavaScript APIs.


User Identifier Requirements
============================

The requirements for a User Identifier are as follows:

* Uniqueness: the identifier must not represent more than one user.

* The identifier must map back to other information about the user (e.g. demographics, enrollments, etc.).

* The identifier for a given user should not change.

* The identifier should not contain :doc:`Personally Identifiable Information (PII) <oep-0030-arch-pii-markup-and-auditing>`.

* The identifier should be consistent across all systems, including the LMS and Studio, mobile apps for iOS and Android, and other Open edX Services like Ecommerce.


User Identifier Candidate Comparison
====================================

The following table summarizes the comparisons of a list of candidate user identifiers.

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Identifier
     - Comments
   * - username
     - * Username is considered PII.
   * - LMS user_id
     - * anonymous (is not considered PII)

       * Segment (used for anlytics) recommends database IDs because they never change.

       * Availability across all services is almost, but not yet complete.

       * Auto-incremented id allows for possible information leakage of auth_user table and ties us down to implementation of the housing database.
   * - email address
     - * Is PII and modifiable by the user.
   * - anonymous user_id
     - * anonymous (is not considered PII)

       * It is currently constructed by hashing the user's *LMS user_id* with the Django server's *SECRET_KEY* value. This value would change when the *SECRET_KEY* is rotated.

As noted above, the *LMS user_id* was selected for internal analytics events as the best match given the requirements. It continues to be the best match for the additional use cases of public events, REST APIs and JavaScript APIs.


Consequences
************

A consequence of the decision to use the *LMS user_id* as the unique user id is that some additional changes are needed to make the *LMS user_id* more globally available across all services:

* The `LMS OAuth Dispatch App`_ will now include the *LMS user_id* in the JWTs (JSON Web Tokens) used as part of the `Open edX Authentication`_ upon request.

* In order to keep the *LMS user_id* anonymous, there must not be any APIs or pages that expose the association between the *LMS user_id* and any of the user’s PII (e.g. username and full name) to unauthorized users.

* Additionally, some further investment is required to make the *LMS user_id* available to all services for all users, like the Ecommerce Service.

This decision is also a simple acknowledgement of our tie to the database implementation and its auto-incremented value, given its already pervasive usage.

The *LMS user_id* is also considered a safe and anonymous option for third-party integrations as described above. One example of a third-party integration where the *LMS user_id* can now safely be used is in the `Realtime Events API`_, which has been updated with this clarification.

Note: The legacy implementations of LTI and the %%USER_ID%% keyword use what is called an "anonymous id", but is no more "anonymous" than the *LMS user_id* from a PII perspective. They use an id made of a combination of user id and course id, which makes it impossible for a third party system to build a user model in its own system across courses, and limits the capabilities a third-party system might offer a user. For future LTI implementations, this decision simply states that the *LMS user_id* is an option unless these additional limitations are a requirement of the integration.

.. _LMS OAuth Dispatch App: https://github.com/edx/edx-platform/blob/master/openedx/core/djangoapps/oauth_dispatch/docs/README.rst
.. _Open edX Authentication: https://openedx.atlassian.net/wiki/spaces/PLAT/pages/160912480/Open+edX+Authentication
.. _Realtime Events API: oep-0026-arch-realtime-events.rst


Rejected Alternative: a new UUID
********************************

If we were to try to eliminate the drawbacks of using an auto-incremented *LMS user_id* as our unique identifier, an alternative might be to introduce a new id, like a UUID (Universally Unique IDentifier).

However, the introduction of a new id has large costs:

* Introducing a brand new id as the standard user identifier would mean starting from scratch regarding compliance. The effort to reach even the current level of compliance with use of the *LMS user_id* throughout the Open edX system would be large.

* Data analytics is one area where, even though we don't have 100% compliance with use of the *LMS user_id*, we have enough compliance to make the data useful. Switching the user identifier for data analytics would require a large coordinated effort that would be difficult for data scientists with no benefit to them.

* Adding another user id without being able to retire an old id also has the drawback of making the system less approachable, as each developer tries to learn which id to use in which situation.
