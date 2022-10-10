OEP-15: Course-wide Custom JavaScript
#####################################

+---------------+----------------------------------------------------+
| OEP           | :doc:`OEP-15 <oep-0015-arch-course-wide-js>`       |
+---------------+----------------------------------------------------+
| Title         | Course-wide Custom JavaScript                      |
+---------------+----------------------------------------------------+
| Last Modified | 2017-06-01                                         |
+---------------+----------------------------------------------------+
| Author        | Martin Segado <msegado@mit.edu>                    |
+---------------+----------------------------------------------------+
| Arbiter       | Scott Dunn <sdunn@edx.org>                         |
+---------------+----------------------------------------------------+
| Status        | Provisional                                        |
+---------------+----------------------------------------------------+
| Type          | Architecture                                       |
+---------------+----------------------------------------------------+
| Created       | 2016-08-29                                         |
+---------------+----------------------------------------------------+
| Resolution    | `Original pull request`_                           |
+---------------+----------------------------------------------------+
| References    | - `Initial OEP suggestion`_ on the edx-code list   |
|               | - `An edx-code question`_ from another user at MIT |
|               |   who was hoping to include course-wide JS/CSS     |
+---------------+----------------------------------------------------+

.. _Original pull request: https://github.com/openedx/open-edx-proposals/pull/43
.. _Initial OEP suggestion: https://groups.google.com/d/topic/edx-code/T83TDxhH74E/discussion
.. _An edx-code question: https://groups.google.com/d/topic/edx-code/idjPWUIx8Ls/discussion

Abstract
********

The Open edX platform is a powerful tool for both content authoring and learning-sciences research, in part due to its support for including arbitrary JavaScript in course materials; for example, such scripts can enable researchers to conduct novel experiments and log custom learner interaction events for analysis. Unfortunately, deploying and maintaining JavaScript assets across an entire course (or multiple courses) is a significant challenge, especially in Studio-based workflows where :code:`<script type="text/javascript">` elements must be included manually in every vertical. As an alternative, this OEP proposes a mechanism for authors to include custom JavaScript across an entire course using a policy key. The implementation is fully backward-compatible and leverages existing XBlock infrastructure to include scripts in the LMS.

Motivation
**********

The primary goal of this OEP is to facilitate research and experimentation by removing the barriers to large-scale JavaScript inclusion. Simplifying the inclusion of scripts is especially important for experiments spanning many courses (e.g., all MITx courses): it is not reasonable to expect all course teams at an institution to manually modify every single vertical in their course to include a research script as is the case with the current platform, but it is quite feasible to request that they make a single easy-to-reverse change to their course policy.

As an additional benefit, a course-wide JavaScript mechanism would also simplify the use of course- or institution-wide utility scripts; HarvardX, for example, maintains a custom JS library for course authors.

Specification
*************

The proposed implementation adds new policy keys for custom course-wide scripts, `course_wide_js` for adding JavaScript, and `course_wide_css` for adding CSS code. These both take a list of URLs to JavaScript and CSS resources respectively.

.. code-block:: json

   [
       "//lms.site.com/asset-v1:edX+DemoX+Demo_Course+type@asset+block@script.js",
       "/asset-v1:edX+DemoX+Demo_Course+type@asset+block@script.js",
       "//some.cdn.com/library.js"
   ]

These resources will then be directly loaded in all pages in the course by including them in the base templates.

Rationale
*********

The approach above requires very little code for a working implementation as can be seen in the `related PR`_.

For the user-facing portion of the feature, course policy settings (which would appear on the *Advanced Settings* page of edX Studio) seem like a good fit:

- Research script inclusion fits well within the `existing description and warning`_ on the Studio *Advanced Settings* page:

    **What do advanced settings do?**

    Advanced settings control specific course functionality. On this page, you can edit manual policies, which are JSON-based key and value pairs that control specific course settings.

    Any policies you modify here override all other information you've defined elsewhere in Studio. Do not edit policies unless you are familiar with both their purpose and syntax.

- A policy setting requires no additional UI (display and editing is already handled by Studio), reducing implementation and maintenance burden

- Policy settings are conceptually course-wide in scope, which matches the scope of course-wide scripts

.. _related PR: https://github.com/openedx/edx-platform/pull/28411
.. _existing description and warning: https://github.com/openedx/edx-platform/blob/d497e194623dd32ad5a66f141529129267db645c/cms/templates/settings_advanced.html#L83-L86

Why not XBlocks?
================

Custom XBlocks cannot be instantiated at the top level of the courseware; as with script tags, they would need to be added to every vertical in a course for course-wide experiments. The `XBlock course tabs`_ idea on edx-code (originally referred to as `course-level XBlock views`_) would permit blocks to be loaded outside of verticals, but these would only be active when a user explicitly navigates to their tab, not when users are interacting with the courseware as required here.

.. _course-level XBlock views: https://groups.google.com/d/topic/edx-code/Xlfof0JFlMo/discussion
.. _XBlock course tabs: https://groups.google.com/d/topic/edx-code/ywjXV0wzQiw/discussion

What about security?
====================

The open edX platform already permits arbitrary JavaScript in course content via :code:`<script type="text/javascript">` tags, and such scripts may already be deployed course-wide (albeit tediously) by duplicating them across every vertical in a course. *This OEP merely aims to make these things easier, not to add abilities which authors do not already have.*

Nevertheless, at least one ability difference does exist between the existing and proposed mechanisms: scripts will execute earlier when associated with the courseware fragment instead of a content block fragment, which would give them slightly more control over the loading of subsequent page resources. The implications of this change should be considered carefully, though the author of this OEP is not currently aware of any vulnerabilities that this would introduce.

It is also possible that this feature could increase script usage overall and thus increase the probability of users creating a vulnerability. To mitigate this risk, the description text for the policy key should carry a stern warning to authors similar to the following:

    **WARNING: Custom scripts gain full access to staff and student user accounts**, including the ability to act as these users on Studio, discussion forums, and instructor dashboards. Use this feature with extreme caution. Only include scripts from experienced, trusted JavaScript authors, and only include scripts hosted on secure trusted servers.

(As an aside, it might be valuable for the existing script-tag feature to carry a similar warning, though that is outside the scope of this OEP.)

Security was briefly touched on in the `initial edx-code thread`_ for this feature. One idea mentioned there (but not yet adopted in this OEP) is to allow only :code:`/static/*.js` scripts; this would prevent exploits in which an attacker compromises a server hosting an included script and replaces it with a malicious script, but it would also differ from the behavior of :code:`<script type="text/javascript">` tags which may use any valid :code:`https://` URL. Another comment raised the idea of a feature flag:

    "We might want to make this feature be something that can be disabled if a given installation is not comfortable with giving this power to its authors. As you point out, the power is already there through multiple other mechanisms, so maybe this isn't a concern."

.. _initial edx-code thread: https://groups.google.com/forum/#!topic/edx-code/T83TDxhH74E/discussion

What about performance?
=======================

Several performance-related points were also raised via edx-code. The first concerns the use of this feature to load JavaScript libraries (e.g., for things like course-wide code syntax highlighting):

    "Having the assets tied to the course means that even if the block [that uses these assets] is removed, the assets would still be loaded. It would be better to have them requested on-demand by only the blocks that need them. Having said that, we don't have a mechanism in XBlock to allow multiple blocks to share the same library."

This is a valid point; there's a risk of vestigial libraries being loaded with this approach. The simplest way to address this concern is to recommend that authors not use this feature for libraries and instead continue loading these using the existing mechanism (i.e., by adding script tags in any blocks where they are needed).

Another comment mentioned the impact of additional HTTP requests:

    "There are performance implications to loading a number of individual files like this. Having said that, it would be difficult to have individual courses contribute files to the static asset pipeline, since courses can be created/imported after the LMS has been stood up."

Some benchmarking will likely be appropriate here, though hopefully the benefits of this feature will outweigh any performance impact if only a few scripts are included. Caching should help to reduce the duration of each request after the initial load: it looks like edX sets the Cache-Control max-age to 1 year, and external JavaScript CDNs should also have reasonable caching behavior. Hopefully HTTP/2 will also help as it gains adoption.

What about compatibility and support?
=====================================

Platform hosts such as edX should make it clear that this is a power-user feature that would carry no support beyond that for current :code:`<script type="text/javascript">` tags (i.e., *the platform guarantees that your scripts will make it into the page, but you're on your own if they don't work or if something breaks due to platform changes*). As with security above, it's possible there will be more complaints or support requests from users simply because of wider script usage, though good documentation and a warning in the policy key description should hopefully keep these to a minimum.

Backward Compatibility
***********************

The proposed feature does not introduce any known backward incompatibilities.

Reference Implementation
************************

- https://github.com/openedx/edx-platform/pull/28411

Rejected Alternatives
*********************

None so far (other than hacks involving scripts to automate the process of modifying every vertical in a course).
