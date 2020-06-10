####
Developer Documentation
####

Overview
********

Documentation can live in lot's of places, we want to be clear about which documentation lives where most of the time so that finding docs is easier.  In general, keep the documents as close to the thing that they are describing as possible.

Details
****

* READMEs, HOW-TOs, API Docs and ADRs should live with the code.

  * API docs should be in swagger format.

  * **Everything else should be RSTs**.  Yes, you already know markdown but RST is more powerful and robust and that is what we have chosen to use for consistency across the whole platform.  This_ is a good resources for learning RST.

* Process docs such as scrum notes, retros, personal notes should not live in the repo and should generally be in confluence.

* Sometimes documents span repos, if a document is relevant to multiple repos that are related to each other, pick the most sensible of the repose to put the doc in.  Link all the relevant repos in the doc and link back from all the repos to the doc to make it easier to find in either direction.

* If a document is relevant to everyone, it should be in the OEP_ repo which is where we document things that are relevant to all of  openedx.  Things like best practices for dependency management, where we should put different kinds of documentation, etc.


All the details
***

The detailed decisions for why we do these things this way and more info about the different kinds of documentation can be found in OEP-19_.

.. OEP_ https://open-edx-proposals.readthedocs.io/en/latest/oep-0001.html
.. This_ https://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html
.. OEP-19_ https://open-edx-proposals.readthedocs.io/en/latest/oep-0019-bp-developer-documentation.html
