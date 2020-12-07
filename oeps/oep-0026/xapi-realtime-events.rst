.. _xapi_realtime_events:

=====================
xAPI Real-time Events
=====================

.. contents::
   :local:
   :depth: 2

Standardized Integration with xAPI
==================================
We have chosen xAPI as one of the primary communication standards for real-time events since it has `widespread industry support and usage`_ for tracking and analyzing learning activity. Please see `xAPI Architecture Overview`_ for further information on xAPI.

.. _widespread industry support and usage: https://xapi.com/adopters/
.. _xAPI Architecture Overview: https://www.adlnet.gov/research/performance-tracking-analysis/experience-api/xapi-architecture-overview/

.. Note:: Although xAPI specifies a standardized format, it is a low-level transaction schema and relies on higher-level "profiles" applied on top of it. So the profiles for specific Activities, Verbs, Contexts, etc used by Open edX need to be contractually maintained. This can be ensured by a community maintained :ref:`oep-26-validator` component.

.. _oep-26-lrs:

Learning Record Store (LRS) - Future
====================================

xAPI includes a specification for a Learning Record Store (LRS), which encapsulates a data store with APIs for storing and accessing xAPI data. While an LRS could be used for many different purposes, it can be used to train adaptive engines as shown in the diagram below.

.. image:: ./adaptive_learning_lrs_basic.png
   :alt: The diagram above is enhanced with a new LRS component that receives events from the Open edX "Eventing" component and is accessed by adaptive engines for training purposes, using xAPI for both transactions.

In the short term, however, we will not implement our own LRS. We will look into integration efforts with third party LRS services.

Statement API
=============

xAPI specifies 4 standard REST-ful JSON payload APIs, of which only the `Statement API`_ is needed for our purposes as that API is sufficient for tracking learning activity.

The Statement API has essentially the following parts: `xAPI Actor`_, `xAPI Verb`_, `xAPI Object`_, `xAPI Context`_, and `xAPI Result`_.

.. _Statement API: https://xapi.com/statements-101/

xAPI Actor
~~~~~~~~~~
Although the **Actor** field can be either an *Agent* or a *Group*, we will primarily support only the *Agent* type, which is used for individuals performing an activity (`xAPI Verb`_ on an `xAPI Object`_).

An Actor can be identified using `Friend of a Friend (FOAF)`_ vocabulary with either: (1) `email address`_, (2) `hash of email address`_, (3) `OpenID URI`_, or (4) `account`_ with a *homepage*-scoped identifier.  One of these is sent along with the Actor's "name". To be mindful of learner privacy, we will initially take a conservative approach and only send #3, with an Open edX anonymized unique identifier of the learner (:ref:`oep-26-user-id`).

In the future, if certain external systems require `Personally Identifiable Information (PII)`_, like the learner's email address or name, then those may be conditionally sent with appropriate permissions. Adaptive engines, however, do not need PII.

Initially, we will exclude the "name" field. However, if we find that xAPI JSON parsers assume this field always exists, then we can include the field but provide a non-PII value, such as a copy of the :ref:`oep-26-user-id`.

Example
^^^^^^^

Here is an example of an **Actor** JSON value that we would generate:

::

    "actor": {
        "objectType": "Agent",
        "openid": "https://openedx.org/users/user-v1:<anonymized-user-id>",
        "name": "https://openedx.org/users/user-v1:<anonymized-user-id>"  # only include this field if necessary
    }

See `Deep Dive: Actor/Agent`_ for more information on xAPI Actors.

.. _Friend of a Friend (FOAF): http://xmlns.com/foaf/spec/
.. _email address: http://xmlns.com/foaf/spec/#term_mbox
.. _hash of email address: http://xmlns.com/foaf/spec/#term_mbox_sha1sum
.. _OpenID URI: http://xmlns.com/foaf/spec/#term_openid
.. _account: http://xmlns.com/foaf/spec/#term_account
.. _Personally Identifiable Information (PII): https://en.wikipedia.org/wiki/Personally_identifiable_information
.. _`Deep Dive: Actor/Agent`: https://xapi.com/deep-dive-actor-agent/

xAPI Verb
~~~~~~~~~

The **Verb** in xAPI is a past tensed value, identified by a URI from the `xAPI registry`_ and paired with a short display string. It denotes the action that was performed by the Actor on the Object in the statement. As best as possible, we will use standard and registered Verbs rather than creating custom ones. The chosen Verbs are documented in the `Open edX Events`_ section below.

The registry is automatically created from multiple profiles. For now, we will limit ourselves to only URIs prefixed by the following domains, in the following priority order (in case of conflicting names):

* http://adlnet.gov
* http://w3id.org
* http://id.tincanapi.com

If, by any chance, a verb needed by Open edX does not exist in the registry, then we will create a pull request to recommend adding it to the `central GitHub repository of xAPI Profiles`_. 

Here is an example of a **Verb** JSON value that we would generate:

::

    "verb": {
        "id": "http://adlnet.gov/expapi/verbs/answered"
    }

.. Note:: To keep the size of events as small as possible, we choose to avoid extraneous fields. For example, we intentionally exclude a "display" field in the example above.

See `Experience API Deep Dive: Verbs`_ for more information on xAPI Verbs.

.. _xAPI registry: http://xapi.vocab.pub/verbs/index.html
.. _central GitHub repository of xAPI Profiles: https://github.com/adlnet/xapi-authored-profiles
.. _`Experience API Deep Dive: Verbs`: https://xapi.com/deep-dive-verb/

xAPI Object
~~~~~~~~~~~

Initially, the **Object** in an Open edX xAPI event will be an xAPI *Activity*, which is uniquely defined by a URI. (In the future, we may expand Objects to also be Actors in case of *social interactions*, and Statements in case of *voiding*.)

The **id** field is a unique identifier. The `Open edX Events`_ section has specifics on which Open edX identifier is used in each event.

The **type** of the Activity and the **name** of the Activity are included in the **definition** field.  Similar to Verbs, the type of the Activity is a standard URI taken from the `xAPI registry`_.

Here is an example of an **Object** JSON value that we would generate:

::

    "object": {
        "id": "https://courses.openedx.org/xblock/block-v1:openedx+origami-folding+1T2018+type@problem+block@abcd",
        "definition": {
            "type": "http://adlnet.gov/expapi/activities/question",
            "name": { 
                "en-US": "Question on mountain fold needed to create an origami crane base",
            }
        }
    }

See `Deep Dive: Object`_ for more information on xAPI Object.

.. _`Deep Dive: Object`: https://xapi.com/deep-dive-object/

xAPI Context
~~~~~~~~~~~~

The **Context** field allows us to embed additional contextual information in each statement. See specifics in the `Open edX Events`_ section below since it varies by event type.

Here is an example of a **Context** JSON value that we would generate:

::

    "context": {
        "registration": "https://openedx.org/enrollments/enrollment-v1:<anonymized-enrollment-id>",
        "contextActivities": {
            "parent": [{
                "objectType": "Activity",
                "id": "https://openedx.org/courses/course-v1:openedx+origami-folding+1T2018"
            }]
        }
    }

See `Deep Dive: Context`_ for more information on xAPI Context.

.. _`Deep Dive: Context`: https://xapi.com/deep-dive-context/

xAPI Result
~~~~~~~~~~~

The **Result** field specifies the score the user earned on an activity.  Here is an example of a JSON value that we would generate for a problem type:

::

    "result": {
        "success": false,
        "completion": true,
        "score": {
            "min": 0,
            "max": 50,
            "raw": 10,
            "scaled": 0.20
        },
        "response": "foo"
    }

See `Deep Dive: Result`_ for more information on xAPI Result.

.. _`Deep Dive: Result`: https://xapi.com/deep-dive-result/

Open edX Events
===============

Currently, the Open edX system supports and maintains events that are sent to tracking logs, as described in `Tracking Log Events`_.

Prioritized List of Events
~~~~~~~~~~~~~~~~~~~~~~~~~~

For this first iteration, we will focus primarily on the following events:

- **Enrollment events**

  + `edx.course.enrollment.activated <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-course-enrollment-activated-and-edx-course-enrollment-deactivated>`_.
       Whenever a learner enrolls in a course.
  + `edx.course.enrollment.deactivated <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-course-enrollment-activated-and-edx-course-enrollment-deactivated>`_.
       Whenever a learner unenrolls from a course.

- **Problem interaction events**

  + `edx.grades.problem.submitted <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/course_team_event_types.html#edx-grades-problem-submitted>`_.
      Whenever a learner submits any problem.
  + `problem_check <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#problem-check>`_.
       Whenever a learner's answer to a problem is checked.
  + `showanswer <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#showanswer>`_.
       Whenever a learner is shown the answer to a problem.
  + `edx.problem.hint.demandhint_displayed <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-problem-hint-demandhint-displayed>`_.
       Whenever a learner requests a hint to a problem.

- **Video events**

  + `edx.video.loaded <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#load-video-edx-video-loaded>`_.
       Whenever a learner loads a video.
  + `edx.video.played <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#play-video-edx-video-played>`_.
       Whenever a learner plays a video.
  + `edx.video.stopped <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#stop-video-edx-video-stopped>`_.
       Whenever a learner stops a video.
  + `edx.video.paused <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#pause-video-edx-video-paused>`_.
       Whenever a learner pauses a video.
  + `edx.video.position.changed <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#seek-video-edx-video-position-changed>`_.
       Whenever a learner navigates to a different position in a video.

- **Course navigation events**

  + `edx.ui.lms.sequence.outline.selected <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-ui-lms-outline-selected>`_.
       Whenever a learner navigates to a subsection in the course.
  + `edx.ui.lms.sequence.next_selected <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#example-edx-ui-lms-sequence-next-selected-events>`_.
       Whenever a learner navigates to the next content in the course.
  + `edx.ui.lms.sequence.previous_selected <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-ui-lms-sequence-previous-selected>`_.
       Whenever a learner navigates to the previous content in the course.
  + `edx.ui.lms.sequence.tab_selected <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-ui-lms-sequence-tab-selected>`_.
       Whenever a learner navigates to another unit within a subsection.
  + `edx.ui.lms.link_clicked <http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/student_event_types.html#edx-ui-lms-link-clicked>`_.
       Whenever a learner clicks on any link in the course.

.. _Tracking Log Events: http://edx.readthedocs.io/projects/devdata/en/latest/internal_data_formats/tracking_logs/index.html

Event Field Mapping
~~~~~~~~~~~~~~~~~~~

Please see the `Open edx xAPI Events`_ document for a detailed view of the mapping between the above Open edX events and their equivalent Open edX xAPI formats.

.. _Open edx xAPI Events: https://docs.google.com/spreadsheets/d/1Qx-1NkpCHXkWh8AagwHD5vzyBCdRXQVof10Ent_EDms/edit?usp=sharing

Implementation Note
~~~~~~~~~~~~~~~~~~~

TBD - The development team will assess whether we will use (and start owning) the already implemented (but no longer maintained) `xAPI Python Open Source Library`_. 

.. _xAPI Python Open Source Library: https://xapi.com/python-library/
