===========================
OEP-33: Code Autoformatting
===========================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-33 </oeps/oep-0033-bp-code-autoformatting>`  |
+-----------------+--------------------------------------------------------+
| Title           | Code Autoformatting                                    |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2019-03-04                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Calen Pennington <cale@edx.org>                        |
+-----------------+--------------------------------------------------------+
| Arbiter         | Ned Batchelder <ned@edx.org> (Tentatively)             |
+-----------------+--------------------------------------------------------+
| Status          | Draft                                                  |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2019-03-04                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | 2019-03-04 - 2019-04-01                                |
+-----------------+--------------------------------------------------------+

Context
-------

In a large software project or ecosystem like the OpenEdX codebase, consistency
of code formatting makes it easier to read the code. In an ecosystem with many
contributors, maintaining that consistency requires automated tools (such as
linters), consistent code review, or both. In general, minimizing the number
of review comments that a reviewer has to make on an incoming change makes
review easier, and lessens the number of back-and-forths required to merge
the code. As such, linters that can be run locally should be preferred.
Similarly, ambiguity in style guidelines should be minimized, so that there
are fewer places for commentary around what amounts to personal preference
(which might then instigate unnecessary debate).

In short, more consistent, less ambiguous standards for code formatting
make for more productive code reviews and waste less time on inconsequential
details.

Decision
--------

We will select automatic code formatters for all of our major languages. These
formatters will have both a lint-mode (that will be run in continuous
integration to ensure consistent formatting) and an autoformat mode (which
can be integrated with editors to free developers from having to think about
code formatting). These formatting standards will be applied across all
OpenEdX-authored repositories.

Selected tools should, ideally, require minimal configuration. Any required
configuration should be added to `edx/edx-lint`_ in order to allow it to
be centralized and standardized across repositories in the OpenEdX ecosystem.

The current recommended formatters are:

    - Python: `black`_
    - Javascript: `prettier`_
    - CSS/SASS: `prettier`_
    - HTML: `prettier`_
    - JSON: `prettier`_
    - PHP: `prettier-php`_

.. _black: https://github.com/ambv/black
.. _prettier: https://prettier.io/
.. _prettier-php: https://github.com/prettier/plugin-php
.. _`edx/edx-lint`: https://github.com/edx/edx-lint

Consequences
------------

There will be commits to all OpenEdX repositories that cause large-scale
reformatting without functional changes. This will muddy the blame-file
history. Users with auto-formatters configured in their editors working
in a repo that is not yet OEP-33 compliant will need to disable their
autoformatter, or risk large-scale changes that are unrelated to their
current tasks. All OpenEdX code will be formatted consistently and
unambiguously. Developers will not have arguments about
code layout.

References
----------

`gofmt`_ was the first code-autoformatter to gain wide-scale acceptance.

.. _gofmt: https://blog.golang.org/go-fmt-your-code

`Black editor integration`_
`Prettier editor integration`_

.. _Black editor integration: https://github.com/ambv/black#editor-integration
.. _Prettier editor integration: https://prettier.io/docs/en/editors.html

An example ``tox.ini``:

.. code-block::

    [testenv:format]
    basepython =
        python3.6
    deps=black
    commands=
        black .

    [testenv:check-format]
    basepython =
        python3.6
    deps=black
    commands=
        black . --check

