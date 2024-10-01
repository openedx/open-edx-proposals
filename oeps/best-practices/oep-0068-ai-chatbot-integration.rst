OEP-68: AI-Driven Chatbot Integration for Enhanced User Support
###############################################################

.. list-table::
   :widths: 25 75

   * - OEP
     - :doc:`OEP-68 <oep-0068-ai-chatbot-integration>`
   * - Title
     - AI-Driven Chatbot Integration for Enhanced User Support
   * - Last modified
     - 2024-10-01
   * - Authors
     - Abstract-Technology <r.pansuriya@abstract-technology.de> <stefania.trabucchi@abstract-technology.de>
   * - Arbiter 
     - <TBD>
   * - Status
     - Draft
   * - Type
     - Best Practice
   * - Created
     - 2024-09-23
   * - Review Period
     - 2024-10-01-2024-11-30
   * - Resolution
     -
   * - References
     -

.. contents::
   :local:
   :depth: 2
  
Abstract
********

This proposal aims to introduce an AI-driven chatbot system integrated into the Open edX platform, built with Django, to assist learners and educators. The chatbot's primary function is to respond to platform-specific questions, offer course recommendations, generate quizzes, provide course details, provide course summary and assist with course progress tracking. The chatbot leverages large language models (LLMs) from OpenAI, with support for natural language processing (NLP) techniques such as TF-IDF and fuzzy string matching.

Motivation
**********

As online education platforms like Open edX continue to grow, the need for scalable support solutions becomes critical. Manually responding to user queries, while effective, is time-consuming and not easily scalable as the user base expands. This proposal seeks to implement an AI-driven chatbot that can automate common support tasks, such as course queries, platform navigation assistance, and course recommendations.

The key benefit of this chatbot is that it reduces the reliance on human support, thus improving response times and allowing support teams to focus on more complex issues. 

The chatbot addresses multiple use cases:

a. Course Recommendations: The chatbot suggests relevant courses based on user input, helping learners find courses that match their interests and needs.

b. Course Summary: Users can request concise or detailed summaries of course content, providing a quick overview of what the course covers.

c. Course Details: The chatbot provides specific information about a course, including start/end dates, course partners, and a description.

d. Difficulty Assistance: When a user expresses difficulty with a topic, the chatbot offers support by recommending beginner-friendly resources or courses.

e. Platform-related Questions: The chatbot handles general platform-related inquiries, such as account management, course navigation, and registration.

f. Course Enrollment: It assists users in checking and managing their course enrollment status, ensuring they are enrolled in the correct courses.

g. Quiz Generation: The chatbot generates quizzes based on the course content a user has completed, helping reinforce learning.

h. Course Progress Check: Users can ask for updates on their course progress, and the chatbot provides real-time data on completed units and grades.

i. Discussion Forum Question Handling: The chatbot automatically gathers and responds to student queries from the discussion forum, making it easy for students to find answers



Specification
*************

The proposed AI-driven chatbot will be implemented using Django on the Open edX platform, integrated with OpenAI’s large language models (LLMs) to enhance user support through conversational abilities. The chatbot provides several key features:

General and Platform-Specific Question Detection: 
The system distinguishes between general queries (e.g., "What is AI?") and platform-specific queries (e.g., "How do I enroll in a course?"). General questions are discouraged, and users are guided toward platform-related inquiries.

Course Summarization: 
Using an LLM, the chatbot generates both concise and detailed summaries of course content. Summaries are presented in a user-friendly, HTML-formatted structure with sections like "Overview" and "Key Concepts."

Course Recommendations: 
Based on user input, the system suggests relevant courses from Open edX's course catalog. Fuzzy matching techniques and TF-IDF are used to ensure that recommendations are highly relevant to the user’s interests.

Quiz Generation: 
The chatbot generates dynamic quizzes based on the content a user has completed in a course. These quizzes are designed to reinforce learning outcomes and provide a more interactive learning experience.

Rate Limiting: 
To prevent system overload, the chatbot includes a rate-limiting feature that controls the number of requests a user can make within a specific time frame, ensuring fair and consistent access.

Platform-Related Assistance: 
The chatbot provides support for platform-related questions, such as navigating the system, enrolling in courses, and accessing course materials, helping users get the most out of the Open edX platform.

Course Enrollment: 
Users can inquire about their enrollment status, and the chatbot will inform them of the courses they are currently enrolled in and help manage their enrollments.

Difficulty Assistance: 
If a user expresses difficulty with a course topic, the chatbot offers support by suggesting beginner-friendly courses or additional resources to help the user better understand the concept.

Discussion Forum Question Handling: (Pending) 
The chatbot gathers questions from the discussion forum and provides answers, making it easier for students to find responses to common queries without needing to manually search through threads. This helps improve forum engagement and support.

User Progress Tracking:(pending) 
Users can ask for updates on their course progress, and the chatbot provides real-time data, including completed units, grades, and overall course progress.

Backend: 
The chatbot is built using Django and Open edX APIs for accessing course data. It also uses the Langchain library for building the interaction chain with OpenAI’s GPT-based LLM models. For text processing, it relies on the fuzzywuzzy library for matching user queries with course titles and descriptions, and TfidfVectorizer for content-based similarity comparisons.

Front-End Integration:(Pending)

AI and NLP Integration:

The AI-driven functionality relies on OpenAI’s GPT models for both summarization and conversation. For recommendations, NLP techniques such as TF-IDF and cosine similarity are used to match user input to relevant course content.

User Interface:

Pending

Security Considerations:

Authentication: 
Users must be authenticated to interact with the chatbot, ensuring only valid users can access course data and personalized recommendations.

Rate Limiting: 
Implement rate limiting to prevent abuse and ensure fair usage of the chatbot services.

Missing Features and Pending Implementations:
Discussion Forum Integration (Pending):

Description: The ability for the chatbot to gather questions from the discussion forum and provide answers is not yet implemented.

Impact: This feature would enhance student engagement by making it easier to find answers without manually searching through forum threads.

Plan: We intend to develop an API integration that allows the chatbot to pull questions from the forum and use OPENAI APIs to generate appropriate responses.

User Progress Tracking (Pending):

Description: Real-time tracking of user progress, including completed units, grades, and overall course progress, is a feature that's pending.

Impact: This would provide users with immediate feedback on their learning journey, helping them stay motivated and on track.

Plan: We plan to integrate with Open edX's progress tracking APIs to fetch and display this information upon user request.

Front-End Integration (Pending):

Rationale
*********

The rationale adds to the specification by describing the events or
requirements that led to the proposal, what influenced the design, and why
particular design decisions were made. The rationale could provide evidence
of consensus within the community and discuss important objections or
concerns raised during discussion. It could identify any related work,
for example, how the feature is supported in other systems.

Backward Compatibility
**********************

This statement identifies whether the proposed change is backward compatible.
An OEP that introduces backward incompatibilities must describe the
incompatibilities, with their severity and an explanation of how you propose to
address these incompatibilities.

Reference Implementation
************************

The reference implementation must be completed before any OEP is given "Final"
status, but it need not be completed before the OEP is "Accepted". While there is
merit to the approach of reaching consensus on the specification and rationale
before writing code, the principle of "rough consensus and running code" is
still useful when it comes to resolving many discussions.

Rejected Alternatives
*********************

This statement describes any alternative designs or implementations that were
considered and rejected, and why they were not chosen.

Change History
**************

2024-10-01
==========

* Document created
* `Pull request #638 <https://github.com/openedx/open-edx-proposals/pull/638>`_
