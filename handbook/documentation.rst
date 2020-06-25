####
Developer Documentation
####

Overview
********

* Keep developer documentation as close to the thing you are describing as possible to make it easier to find and maintain.

Details
****

* READMEs, HOW-TOs, API Docs and ADRs should live with the code.

  * API docs should be in swagger format.

* **Everything else should be RSTs**.  This_ is a good resources for learning RST.

* Process docs such as scrum notes, retros, personal notes should not live in the repo and should generally be in confluence.

* If a document is relevant to multiple repos, pick the most sensible repo to put the doc in, and provide back links from the other repos.

* If a document is relevant to a large part of Open edX, it should live in the OEP_ repo.  Things like best practices for dependency management, where we should put different kinds of documentation, etc.


All the details
***

The detailed decisions for why we do these things this way and more info about the different kinds of documentation can be found in OEP-19_.

.. OEP_ https://open-edx-proposals.readthedocs.io/en/latest/oep-0001.html
.. This_ https://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html
.. OEP-19_ https://open-edx-proposals.readthedocs.io/en/latest/oep-0019-bp-developer-documentation.html
