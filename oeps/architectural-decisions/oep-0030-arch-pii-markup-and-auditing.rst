###############################
OEP-30: PII Markup and Auditing
###############################

+---------------+------------------------------------------------------------+
| OEP           | :doc:`OEP-30 <oep-0030-arch-pii-markup-and-auditing>`      |
+---------------+------------------------------------------------------------+
| Title         | Personally Identifiable Information Markup and Auditing    |
+---------------+------------------------------------------------------------+
| Last Modified | 2019-01-24                                                 |
+---------------+------------------------------------------------------------+
| Author        | Brian Mesick <bmesick@edx.org>                             |
+---------------+------------------------------------------------------------+
| Arbiter       | Alex Dusenbury <adusenbury@edx.org>                        |
+---------------+------------------------------------------------------------+
| Status        | Provisional                                                |
+---------------+------------------------------------------------------------+
| Type          | Architecture                                               |
+---------------+------------------------------------------------------------+
| Created       | 2018-08-29                                                 |
+---------------+------------------------------------------------------------+
| Resolution    | - `Original pull request`_                                 |
|               | - `Update pull request`_                                   |
+---------------+------------------------------------------------------------+
| References    | - `NIST Special Publication 800-122 (pdf)`_                |
|               | - `OEP-002`_                                               |
+---------------+------------------------------------------------------------+

.. _Original pull request: https://github.com/edx/open-edx-proposals/pull/81
.. _Update pull request: https://github.com/edx/open-edx-proposals/pull/101
.. _NIST Special Publication 800-122 (pdf): http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-122.pdf
.. _OEP-002: https://open-edx-proposals.readthedocs.io/en/latest/oep-0002-bp-repo-metadata.html

Abstract
########

The Open edX platform and related independently deployable applications (IDAs) must store information about learners that identifies the learner and, in some cases, is private to them. As an entity that stores this type of information we strive to exercise caution and respect for the privacy of the learners who use our services. This OEP defines what we consider to be personally identifying information (PII) and the ways in which we keep track of that information. It also introduces processes that will allow us to explicitly document which information stored in our systems is PII and provides mechanisms for helping developers follow the outlined best practices.

This OEP explicitly does not cover:

- The process of deleting or anonymizing PII for users, also referred to in this document as "retirement" or "forgetting"
- The process for cataloging PII stored in operational logs such as Splunk or New Relic
- The process for cataloging PII stored in non-operational systems such as analytics databases

Motivation
##########

The primary goal of this OEP is to allow Open edX administrators and developers to understand which information needs special handling, and identifies our best practices for how to make those designations obvious. Respectful handling of PII depends on the following principles, which must be measured against our ability to run the service and meet our research goals and partner obligations:

#. Storing the minimum personal data necessary
#. Sharing the minimum personal data necessary
#. Removing the maximum amount of stored personal information when requested
#. Ensuring our 3rd party partners remove the maximum amount of stored personal information when requested
#. Auditing and cataloging the information we already store to ensure that we are currently meeting our obligations
#. Guarding against accidental storage of PII
#. Guarding against adding storage, processing, or transmission of PII without a means of retiring it

Definitions
###########

PII
***
Open edX has been using `NIST Special Publication 800-122 (pdf)`_ as our operating guideline for what is and is not PII. In some cases we have decided additional information, such as forum posts, may contain PII and ruled that we should assume the stance that best protects the learner.

Information that is considered PII in Open edX includes, but is not limited to:

- Name (first, last, middle initial, prefix, suffix)
- Username
- Password
- Addresses (mailing, billing, work, etc.)
- Phone number
- Email address
- Date of birth (including partial such as year of birth)
- IP addresses
- Job title
- Gender
- Sex
- Social media accounts
- Website
- Biography fields (free-form text fields intended to introduce one's self)
- Forum posts
- Notes service entries

Each case where we store data relating to a learner should be considered individually as to whether that information alone, or combined with other stored information, can be used to identify a learner or violate their privacy. In some cases PII must be kept for legal reasons that supersede the need to forget. If there is any doubt about what constitutes PII or whether a specific piece of information can be safely forgotten, seek legal assistance.

Open edX Ecosystem
******************
Used in this document the phrase "Open edX Ecosystem" include the services that comprise and support the running of an Open edX installation (ex. edx.org). This includes:

- The LMS and Studio
- IDAs such as Notes, Ecommerce, and Forums
- Backend systems such as data analytics and operational logging
- Third party services used to extend functionality of the services such as Optimizely, Google Analytics, and Sailthru

Retirement
**********
Retirement is the Open edX process for "forgetting" a learner. It is triggered at the learner's request, and runs a sequence of automated steps to remove or anonymize learner PII across the Open edX ecosystem, in addition to alerting partners and 3rd party partners that they also need to forget any learner data they may have associated with the learner.

The retirement process is designed to be flexible and extendable based on the needs of each Open edX installation. For instance, to retire new PII that is being added to an LMS database the developer may just need to add a method that listens to one of the existing LMS retirement Django signals in order to be added to the process. The retirement code and process are not part of this OEP and will be documented elsewhere.

Specification
#############

Developer Responsibility
************************
The responsibility for identifying and appropriately labeling PII rests on the developers working in Open edX code. When any new information is being saved to a persistent storage medium (ex. MySQL, Mongo, S3, reporting services, 3rd party marketing tools) the developer must identify whether any of that information may be (alone, or in conjunction with other stored data) PII, seeking legal assistance if necessary. Specifically the developer's responsibilities are:

- Avoid storing PII when it is not necessary
- Ensure that any PII that is stored will be retired upon learner request *before* that information is stored in a production environment
    - Exceptions may be made for classes of PII that need to be kept for legal, financial auditing, or research purposes. Consult legal counsel for approval and annotate appropriately if you encounter such a case.
- Ensure that any PII that is stored is annotated appropriately (see `Docstring Annotations`_ for details)
- Run the PII documentation tool to update the PII documentation when you add, remove, or update a PII annotation (see `Documentation Tooling`_ for details)
- Maintain openedx.yaml to keep the PII repository state up to date (see `Repository Maintenance`_ for details)

Code Reviewer Responsibility
****************************
It becomes the responsibility of code reviewers to confirm the developer assertions about the presence of PII in their pull request are accurate, and that retirement steps and annotations are present and correct when necessary. This is especially important with pull requests coming from outside of edX, where the original developer may not know of this OEP and their responsibilities in regards to PII.

Responsibility for Third-party Service Integrations
***************************************************
When dealing with third-parties that may store PII (ex. Optimizely, Google Analytics, Sailthru) the implementing developer(s) or team members should work with the drivers of the feature and legal counsel to ensure that:

- The third party has a legitimate need for that information to provide the necessary service
- We send only the minimum necessary information to meet the goals of the feature
- The third party has an automated, usable way to request that they forget individual learner data (or has a retention policy that results in the routine purging of such data within an acceptable period of time)
- The retirement process is updated to include the third party's retirement API before the feature is launched

Github Pull Request Templates
*****************************
In order to assist developers in remembering to check all new data for PII, each Open edX repository that might store such data will have a GitHub pull request template that reminds the developer and reviewers to check for the addition of such data in their commits and asks them to affirmatively state that either no such data exists or that it does exist and that appropriate retirement steps are, or will be, ready to retire that data before the request is merged.

Repository Maintenance
**********************
Per `OEP-002`_ all Open edX repositories the `openedx.yaml` files containing metadata about the repository must be updated to contain the OEP state for this OEP inside the `oeps` dictionary. If a repository does not store PII it may simply mark `oep-0030: False` or `applicable: False` with a `reason` as outlined in the OEP-002 specification. The tooling that will inform and enforce our compliance with this OEP will rely on this metadata to determine which repositories to look at so it is vital that these values be kept up to date.

The automatically run tooling should verify the presence and accuracy of `openedx.yaml`.

Docstring Annotations
*********************
**Python**

When adding or modifying **any** data storing models (ex. Django model, MongoDB model) an annotation must be added stating whether the model does or does not store PII. The annotation should be added to the comments of the storage class where the data storage is defined, or the calling method / function if there is no storage class. Calls to third-party services that store data must be annotated to indicate the presence of PII.

It is important to note that under this OEP all Django model classes must be annotated with an assertion of PII / no PII to enable enforcement (see `Enforcement Tooling`_).

These annotations should take the form of Sphinx-style docstrings. In the case where PII is present, the following group of 3 annotations must be used together:

    .. pii: <required description of the PII>

    .. pii_types: <comma separated list of the types of PII stored here, required if the pii annotation exists>

    .. pii_retirement: <comma separated list of retirement types, required if the pii annotation exists>

In the case where no PII exists in a Django model, the following single annotation is used:

    .. no_pii: <optional description>

The potential values of `pii_types` are:

- name (used for any part of the user's name)
- username
- password
- location (used for any part of any type address or country stored)
- phone_number (used for phone or fax numbers)
- email_address
- birth_date (used for any part of a stored birth date)
- ip (IP address)
- external_service (used for external service ids or links such as social media links or usernames, website links, etc)
- biography (any type of free-form biography field)
- gender
- sex
- image
- video
- other (any identifying information not covered by other types, should be specified in the `pii` annotation)

The potential values of `pii_retirement` are:

- retained (intentionally kept for legal reasons)
- local_api (information can be retired using an API/code which exists in this repository)
- consumer_api (information must be retired in the encompassing project which must implement an API/code for retiring this information)
- third_party (information must be retired using an existing third party API)

These can be combined, so that a library that has a retirement API built in, but that requires integration into the consuming application would have `.. pii_retirement: local_api, consumer_api`. Spaces between the entries are optional.

Example 1::

    class ApiAccessRequest(TimeStampedModel):
        """
        Model to track API access for a user.

        .. pii: Stores website and employer information about a linked User.
        .. pii_types: external_service, other
        .. pii_retirement: local_api
        """

Example 2::

    class NoPiiHere(Model):
        """
        This is an example model.

        .. no_pii:
        """

If a project requires another project which stores PII, such as Segment being used in edx-platform, the developer must annotate the place(s) in code where that package is being called to store the PII with the same docstring annotation as if it were a storage class.

Example 3::

    # ..pii: Learner email is sent to Segment in the following line and will be associated with analytics data. We wrap the Segment retirement call in the retire_mailings endpoint.
    # ..pii_types: email
    # ..pii_retirement: local_api, third_party

The goal of this is to allow creation of `Documentation Tooling`_ which will automatically create documentation listing all of the known locations of PII in each repository.

**Javascript**

When adding in Javascript that results in storage of PII to a location that is not covered by other annotations (ex. Segment), annotations should be added to the location(s) in script where the data is being sent. The annotations should take the same form as in Python as Sphinx can also operate on Javascript for documentation.

Example 1::

    % if settings.LMS_SEGMENT_KEY:
        <!-- begin segment footer -->
        <!-- .. pii: The user is identified to Segment by username and email here. See Segment documentation for details. The Segment retirement call is wrapped in the retire_mailings endpoint.
             .. pii_types: username, email_address
             .. pii_retirement: local_api, third_party
         -->
        <script type="text/javascript">
        % if user.is_authenticated:
            ...

Example 2::

    <script type="text/javascript">
    // .. pii: The user's email address is sent to the billing provider here. This information is not retired as it is necessary to keep for legal and financial reporting reasons.
    // .. pii_types: email_address
    // .. pii_retirement: retained
    </script>


Example 3::

    <script type="text/javascript">
    /*
        .. pii: Updates the user's email address with our email marketing provider. Retired in the retire_mailings endpoint.
        .. pii_types: id, email_address
        .. pii_retirement: local_api
    */
    </script>

**Other Cases**

It is likely that other use cases will come up that encompass new languages and storage. In those cases attempts should be made to make those cases match the designs laid out here for making PII locations auditable at the repository level and this OEP should be updated to include best practices for the new case.

Enforcement Tooling
*******************
A tool will be created and integrated into the Open edX test / build systems that will examine all Django models in a project and ensure that they have PII annotations. It is acknowledged that this tool will not handle all cases where PII is stored, but represents an effort to enforce best practices on the majority of places where PII is stored in the Open edX ecosystem.

This tool will instantiate a development-like Django environment inside the project and use Django introspection to look at all installed apps and their models for docstrings containing PII. Given that this list will contain many third party packages we will also need to maintain a list of the PII stored in those apps and models. This "safelist" will need to be hand maintained by the developers adding or modifying packages, though the tooling does assist by generating an initial list of packages that need to be vetted. This mechanism will also allow the rollout of the annotations to take place over time across our own packages.

The tool's output will optionally include a report of the repository's model annotation percentage along with details of which models are not covered, and fail if the repository does not meet a configurable minimum percentage. These potential coverage failures will allow us to track and prioritize the annotization process.

Documentation Tooling
*********************
A tool will be created that reads the annotations in each PII-containing repository and generates a reStructuredText (reST) file named ``pii.rst`` which will be located at the top level directory of the repository or with the repository's documentation and linked to from the top-level README file. This file will gather all of the PII annotations for the project in one place so that the PII load of any given project can be quickly seen and understood. Projects that do not have PII may have their top level README file updated to reflect that.

The tools should also export the list of annotations into a JSON-formatted file named ``pii.json`` which will allow downstream consumers of the data, such as reporting, to discover changes in PII and adjust their own cleanup processes to include the new data.

This tool should be run as part of the test or build processes (depending on project needs) and diff'd against the current version to confirm that the RST and JSON files are up to date.

It is desirable for this tool to use static analysis of the files (instead of executing in a runtime context such as in unit tests) to make sure that all files are examined, and to prevent missing annotations in cases where configuration changes can exclude or break imports.

Organization-wide Tooling
*************************
A tool will be created or enhanced that will be usable at the Github organization level to provide org-wide insight into our stored PII. It should be a wrapper around the Documentation tool, allowing all repos in an org to be cloned and searched for annotations. The tool will also optionally verify the presence of a `openedx.yaml` file in the top level of the repository and verify that its `oep-30` dictionary matches the state of the repository.

Backporting Annotations
***********************
Annotations will need to be added to existing code across the Open edX ecosystem. It is acknowledged that this is significant work, but is beyond the scope of this OEP to determine the resourcing and timing of this effort. It is possible within the framework presented in this OEP to roll out a partial implementation of annotations and expand on it over time.

Rationale
#########
Storing new PII is a decision that should be carefully considered and taken seriously. It is important to the Open edX community that PII be treated with respect, and part of that respect is being able to audit what PII is being stored inside the Open edX ecosystem, where it is being stored, and how that information is removed when a learner requests it.

Processes
*********
The new processes for developers and reviewers represent the least invasive methods that we could devise to track this vital information with the accuracy it deserves. Developers are in the best position to know the context of the data that they are integrating, and are most empowered to call out the locations of that data storage close to the point of use. Developers also have the context necessary to best know how to retire the data that they are storing and whether deletion or anonymization is the best approach to use.

The blocking nature of this process prevents complicated scenarios where learners may have completed the retirement process, but still have recently-added PII data stored in Open edX.

Annotations
***********
Several ways of making the locations of PII storage auditable were tested in forming this OEP (see `Rejected Alternatives`_). Annotations have the following benefits:

- Clearly show PII locations when working with source
- Set us up for easily putting this information into automatically generated documentation in the future
- Do not create Django migrations
- Do not incur runtime costs
- Are relatively low-effort to implement and maintain
- Have a very low likelihood of causing bugs

Sphinx-style annotations were chosen due to Sphinx's wide adoption in the Python, Django, and edX ecosystems. While we have had challenges using Sphinx to document edx-platform, several other Open edX repositories already use Sphinx to generate documentation. Even if we never update edx-platform to use Sphinx these identifiers are unique enough to allow us to audit them with a high degree of confidence.

Tooling
*******
Existing documentation tools were examined in the discovery process of this OEP (see `Rejected Alternatives`_). Based on the problems encountered in those tests no existing project seems to fit our specific needs. A custom solution allows us the flexibility to meet all of the requirements necessary to protect learner privacy without the complications of making larger documentation tools work for our various repositories and complicated build / test systems.

Backward Compatibility
######################
The proposed updates do not introduce any known backward incompatibilities, but would require a comprehensive effort to annotate existing PII in all Open edX repositories. The desire for that effort is what drove the initial tasks that led to this OEP, so this is not undesirable or duplicate work.

Reference Implementation
########################
The `Code Annotations`_ project is a reference implementation containing working versions of the Enforcement Tool (called the Django Model Search Tool) and Documentation Tool (called the Static Search Tool). Documentation on how to use Code Annotations and implementation specific details can be found here: https://code-annotations.readthedocs.org/

The `Organization-wide Tooling`_ does not yet have a reference implementation.

.. _Code Annotations: https://github.com/edx/code-annotations


Rejected Alternatives
#####################
Sphinx & Plugin
***************
An attempt was made to use `Sphinx <http://www.sphinx-doc.org/en/master/index.html>`_ to parse all of the docstrings in edx-platform for the custom ``.. pii:`` tag. While we were able to run Sphinx against the platform and create a plugin that highlighted PII, as well as a special page to view all PII found, the complexities of edx-platform configuration and Sphinx's need to import all modules created a number of errors that cause Sphinx to miss many annotations. Problems like mutually exclusive settings for LMS and CMS were not able to be resolved. Due to the critical nature of this data we are not comfortable offering an option that may miss annotations due to changes in configuration or code.

This option may be workable with significant time investment and significant changes to edx-platform configuration, but would still not put the list of PII front-and-center in the repository. If we make a major push to get the platform Sphinx-compliant and this OEP is accepted, the PII annotation functionality would still be trivially workable in Sphinx as another way to view PII annotations.

Doxygen & Plugin
****************
Due to the import issues with Sphinx a short test was made to use `Doxygen <https://www.stack.nl/~dimitri/doxygen/>`_, a documentation generator that uses static code analysis, to generate the annotation list. This was able to be accomplished in short order by creating a Doxygen extension and with minor modifications to the default templates. Doxygen generated nice, comprehensive documentation of the platform without the issues Sphinx had, as well as XML output of those docs, but has the following drawbacks:

- An additional 3rd party dependency to be added to several systems
- Slow (took about 15 mins to generate docs)
- Output format of the overall docs is nice, but the PII specific output was confusing and not correctly linked
- Supports Python, but not Javascript

Model Annotations
*****************
Experimentation was done to try to use modifications directly to Django models instead of comment annotations for marking PII. Various attempts at adding metadata fell afoul of Django's desire to avoid that kind of functionality. Almost all attempts caused new migrations to be created, which is far from optimal given the number of places we will need to annotate. Others required creating unnecessary fields on the models or wrapping model definitions in hacky context managers to allow custom Meta class variables to be set. This also would not work for PII stored in third parties solely via Javascript.

django-scrub-pii
****************
`django-scrub-pii <https://github.com/MatthewWilkes/django-scrub-pii>`_ is a defunct project that had some potentially useful ideas, and was the only thing close to what we're looking for that seems to exist in the Django ecosystem. Unfortunately it only works on Django models, requires the Meta model context manager hack, and is designed only for creating a dump-sanitize-and-load SQL script that would not work for us.
