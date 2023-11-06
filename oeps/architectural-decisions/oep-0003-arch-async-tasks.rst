OEP-3: Asynchronous Task Management
###################################

+---------------+-------------------------------------------+
| OEP           | :doc:`oep-0003-arch-async-tasks`          |
+---------------+-------------------------------------------+
| Title         | Asynchronous Task Management              |
+---------------+-------------------------------------------+
| Last-Modified | 2016-10-13                                |
+---------------+-------------------------------------------+
| Author        | Jeremy Bowman <jbowman@edx.org>           |
+---------------+-------------------------------------------+
| Arbiter       | Eddie Fagin <eddie@edx.org>               |
+---------------+-------------------------------------------+
| Status        | Provisional                               |
+---------------+-------------------------------------------+
| Type          | Architecture                              |
+---------------+-------------------------------------------+
| Created       | 2016-06-09                                |
+---------------+-------------------------------------------+

Abstract
********

Proposes a system which allows users to initiate, monitor, and retrieve the
results of various long-running tasks in a consistent manner.  This system
leverages existing libraries where practical and makes the development of new
asynchronous tasks straightforward.


Motivation
**********

Course import and export are currently implemented such that they occur in the
scope of a web request, but often take long enough to time out.  This is not
only annoying for the user, but can lose the result of an export or leave the
user uncertain of whether it's safe to start editing the newly imported course
yet.  Clearly the work should be done asynchronously, but we do not yet have a
standard solution for asynchronous tasks which allow the user to track their
progress and/or retrieve output upon completion.  Upon further discussion, it
became clear that other such asynchronous tasks, existing and planned, would
benefit from a consistent framework.  Examples include:

* Re-running a course
* Sending bulk email to course participants
* Generating certificates
* Grading (rescoring, reset attempts, delete state, calculated grades CSV)
* Data downloads (grades, student profiles)
* Open Response Assessment
* Discussion/forum notifier
* Uploading and encoding videos (Video Abstraction Layer, VEDA)


Specification
*************

A new open source Django application will be written to support a user
interface for managing celery tasks in a consistent manner across services.
The main goals of the application are, roughly in order:

1. Allow a long-running task triggered by a user to be run asynchronously,
   while it is clear to the user that they are free to do other work on the
   site in the meantime
2. Allow the user to fetch any output of the task after it finishes
3. Notify the user when the task is complete
4. Give the user some indication of how far along an in-progress task is
5. Support prevention of multiple identical tasks from executing concurrently

Specifically, the initial implementation is expected to have the following
features:

* Celery will be used to run the asynchronous tasks.
* Database models will be used to represent tasks as well as their input,
  output, and current status.  See the tentative schema presented below.
* Upon requesting the task, the user will be redirected to a page showing
  the current status of that task (which can be reloaded to see if the
  status has changed).  This page will only be accessible by the user who
  requested the task (and system superusers).
* If the task has any output artifacts, they can be retrieved from the task
  status page.  The links to these artifacts will be restricted to the user
  who requested the task; in the case of an artifact stored in S3, the link
  will be generated when the user views the status page and only be valid for
  a configurable duration (to minimize risk of the URL being discovered and
  used by others).
* When the task is complete, the task requester will be sent an email to that
  effect (including a link to the task status page).
* The user will be able to cancel a task. Celery's
  `revoke <https://docs.celeryproject.org/en/latest/userguide/workers.html#revoke-revoking-tasks>`_
  command will be used to cancel a task which hasn't started execution yet,
  and the implementation of this OEP will include an API which tasks should
  use periodically to determine if execution should be aborted.
* Tasks will be automatically deleted (along with any artifacts) after a
  configurable timeout duration.  This will help keep database table sizes
  and disk utilization manageable.
* It will be possible to present a fan-out of Celery tasks instantiated to
  perform work in parallel as a single task from the user's perspective,
  with a meaningful overall progress indicator.  This feature could also
  be used to perform a request as a sequence of tasks, if the overall
  duration of a particular task type could be long enough to interfere with
  timely Celery restarts if it was all done in a single task.

Later enhancements will likely include the following:

* Dynamic updates of the task status page without needing to reload it.
* A dashboard listing all tasks instantiated by the user.
* The ability to delete a completed task and its artifacts (hence removing it
  from the dashboard).
* Push notifications of task completion via other methods in addition to
  email (and a UI for notification preferences).  This would likely be
  contingent on the completion and implementation of another OEP specifically
  for push notifications in Open edX.
* Support for tasks which can be monitored by multiple users with sufficient
  access privileges.  Different permissions could be created for status
  viewing, generated artifact access, the ability to cancel the task, and the
  ability to delete the task.

Course import and export will utilize the initial implementation as soon as
it's ready, and other long-running tasks will switch to use it as needed.


Database Schema
===============

Celery provides a pretty full-featured and well understood platform for
running asynchronous tasks, but celery’s ``AsyncResult`` doesn’t store all
of the information we need for the desired features.  Rather than try to
straddle the data we access across celery’s result backend and the MySQL
database, we’ll just store everything we need in database models and let
celery manage and clean up its own result store as normal.  Presented here is
a suggested initial design for these models (they may be altered over time
via migrations as more features are added).

Each task that we want users to be able to monitor in the UI will have a
corresponding AsynchronousTask record:

* created (DateTimeField)
* modified (DateTimeField)
* user (ForeignKey)
* action (CharField - "rerun", "import", "export", etc.)
* name (CharField, usually auto-generated from the task parameters)
* state (CharField - "Pending", "In Progress", "Succeeded", "Failed",
  "Canceled", etc.)
* finished_steps (PositiveSmallIntegerField)
* total_steps (PositiveSmallIntegerField)
* attempt (PositiveSmallIntegerField, starts at 1 for the first attempt to
  execute the task)

If the task generates information which the user needs to be able to retrieve
once it finishes, they are stored in AsynchronousTaskArtifact records:

* created (DateTimeField)
* task (ForeignKey)
* name (CharField, used to distinguish multiple artifacts generated by the
  same task)
* file (FileField, backed by django-storages)
* url (used if the output is a web page instead of a downloadable file)
* text (used if the output is a relatively small amount of text, such as an
  error message or the ID of a record in an external system)

If there is a desire to impose database constraints on the parameters of a
task or just preserve them for future reference, a suitable model customized
for each task may be added.  For example, CourseRerunParameters might contain:

* task (ForeignKey)
* source_course_key (CourseKeyField)
* course_key (CourseKeyField)
* display_name (CharField)


Rationale
*********

Celery is already in use for other asynchronous tasks, and makes a logical
choice as the underlying task execution engine.  Course import and export
timeouts are already happening relatively often, so there is a desire to keep
the scope of the initial implementation small enough that it can be finished
fairly quickly.

Similar functionality has already been implemented for
`course re-runs <https://github.com/openedx/edx-platform/tree/master/common/djangoapps/course_action_state>`_
and assorted LMS actions such as
`bulk email and grade downloads <https://github.com/openedx/edx-platform/tree/master/lms/djangoapps/instructor_task>`_,
but as one-off implementations that share no code.  The new
implementation should combine the best features of these in a form that can be
easily reused for all the user-triggered asynchronous tasks in any service.

The `Jobtastic <https://policystat.github.io/jobtastic/>`_ add-on for Celery
includes some potentially useful functionality for status tracking and error
handling, but it's not yet clear if it would be suitable to include as-is for
this system or just serve as a source of ideas.  There doesn't seem to be a
good existing library providing any user interface elements for managing
celery tasks which isn't geared towards system administrators, although some
of the code in the task dashboard from
`Flower <https://github.com/mher/flower>`_ may prove useful.


Backwards Compatibility
***********************

* Some documentation that deals with course import and export will need to be
  updated to describe the new workflow.  The task status page should contain
  enough information to be pretty self-explanatory, though.
* There will be migrations to add new tables for task tracking.
* Existing implementations of asynchronous task management will probably be
  gradually switched over to this new system.  There's no particular rush to
  do so, but standardizing on a single implementation should yield a number
  of benefits with respect to functionality and ease of maintenance.  Any
  partners who have implemented similar systems should be given the
  opportunity to offer input on the new implementation, and may desire support
  in switching to it when appropriate.
* There are a lot of asynchronous tasks already in the
  `instructor task <https://github.com/openedx/edx-platform/tree/master/lms/djangoapps/instructor_task>`_
  package of the LMS, so particular care should be taken to produce something
  compatible with that in both code and behavior (in order to simplify the
  aforementioned standardization on a single implementation).  The existing
  code is local to the LMS and makes some assumptions about what kind of tasks
  can be run, so it can't just be used as is.


Change History
**************
